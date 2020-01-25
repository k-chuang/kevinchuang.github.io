---
title: Automate the Menial Tasks Pt.1
date: 2018-04-05 22:03:15.000000000 -07:00
type: post
parent_id: '0'
published: true
password: ''
status: publish
categories:
- Automation
tags:
- Outlook Emailer
- Python
meta:
  timeline_notification: '1522990998'
  _rest_api_published: '1'
  _rest_api_client_id: "-1"
  _publicize_job_id: '16512115756'
  _wpcom_is_markdown: '1'
permalink: "/2018/04/05/automate-the-menial-tasks-pt-1/"
---
These next few blog posts were motivated by [Automate the Boring Stuff](https://automatetheboringstuff.com/)&nbsp;by&nbsp;Al Sweigart, which I highly recommend reading (I haven't personally finished the book yet, but it's one of the first Python books I have read, and has motivated me to work on projects to "automate the boring stuff"). In this post, I will be talking about how to implement a Python Outlook Emailer bot in a Windows 7 environment to automate emailing subjects/volunteers using some cool Python libraries and techniques, and Google sheets.

The GitHub repo for this application will be located here: [Python Outlook Emailer](https://github.com/k-chuang/automate-menial-tasks/blob/master/automate-outlook-emailer.py)

## Python libraries used:

- [pypiwin32](https://github.com/mhammond/pywin32) - a Python library for Windows 32 extensions, which allows access to Windows API (used to interact with Microsoft Outlook)
- [gsheets](https://pypi.python.org/pypi/gsheets/0.3) - a Python wrapper around the Google Sheets API
- [pandas](https://pandas.pydata.org/) - a super awesome library that makes working with data easy. It's good for data manipulation and data analysis, and also creating easy to use data structures.
- [re](https://docs.python.org/2/library/re.html) - a Python library for regular expressions
- [threading](https://docs.python.org/2/library/threading.html) - a Python library for threading

## Setting up

Python 2.7 was used, and the Python libraries and their versions are located in the requirements.txt file.

To install the necessary Python packages, run:

`pip install -r requirements.txt`

I found a nice Python library called **[pipreqs](https://github.com/bndr/pipreqs)** that automatically generates the requirements.txt file based on imports. You just run this simple command in your terminal:

`pipreqs /path/to/project`

I should have used a virtual environment (either&nbsp;**[virtualenv](https://virtualenv.pypa.io/en/stable/)&nbsp;**or **[pipenv](https://docs.pipenv.org/)**)&nbsp;to set up the requirements.txt file for the required Python libraries, but this project was created before I knew about these concepts. However, I am definitely looking into using these tools to create an isolated Python environment for future projects.

Since we are using a Google form and a couple Google sheets, you will need an account on Google to create these forms and sheets. In addition, since we are using the Google Sheets API, you will need to visit the [Google Developer Console](https://console.developers.google.com)&nbsp;to generate the **clients\_secrets.json** and **storage.json** file for authentication purposes. The [documentation for gsheets](https://gsheets.readthedocs.io/en/stable/)&nbsp;has an easy to understand way of setting this up (it's under **Quickstart** ).&nbsp;This step in authentication is necessary in order to use the Google Sheet API.

The Google form should have this format:&nbsp;[https://docs.google.com/forms/d/e/1FAIpQLScNaQrv1L-JAC6RaLAVSrXbhiiTcvTHM-q-zvUeWotelriUxA/viewform?usp=sf\_link](https://docs.google.com/forms/d/e/1FAIpQLScNaQrv1L-JAC6RaLAVSrXbhiiTcvTHM-q-zvUeWotelriUxA/viewform?usp=sf_link)

The Google sheets that the script will prompt for should be as follows:

**Mailing list** : a Google sheet that contains a database of emails and user information that are on the mailing list. IMPORTANT: the 2nd column should be a column of emails.

**Blacklisted emails** : a Google sheet with just one column of emails and nothing else. These are the emails you do not wish to email, and the script will take the necessary precautions to filter out these emails.

## How it works

To avoid making this a long, convoluted step by step guide, I will just give a high level overview of how the script works, and a couple cool features within the script.

The Python script is an automated emailer that is meant to sign subjects up for an appointment to come in a fill out a survey. After users have filled out the survey, the Python script can be run again to send them a confirmation email with their appropriate appointment time and date. There are essentially two main steps to this script that happen sequentially. These steps do not necessarily have to be run sequentially. For example, if you already sent out the Google Form to people via some form of social media or ad posting, and have a filled out Google Sheet response to the form, then you can skip the first step.

**First step:** &nbsp;To start, there is an _initial email_, which emails subjects that are in a mailing list. The email will ask them if they are interested in participating in a survey, and if they are, to sign up for a time slot via a Google Form (Google Form format is located above). When running the script, the prompt will ask you whether or not you are emailing the mailing list (with input "i"), or confirming an email (with input "c"). You would want to type in the letter i to initiate the first step.

The script will then prompt you for other information, such as the url to the Google sheet containing the blacklist (if any), and the url to the Google sheet containing the mailing list. After you have successfully entered these two urls, the script will then prompt you to check the formatting of the email, and if you are satisfied with it. This is mainly to make sure the html body of the email is formatted correctly.

See [initial\_email.html](https://github.com/k-chuang/automate-menial-tasks/blob/master/html/initial_email.html) for the correct formatting of the email body.

After you confirm that the email body is formatted correctly, the script will then prompt you again, and ask if you want to instantly send all emails, or oversee the sending of emails. Overseeing the emails will show you each email being sent with some time (5 seconds) in between sending each email. I recommend running in the oversee mode until you are familiar with the script.

All of the emails you have already sent will be recorded into the _already\_emailed.txt_ file to prevent from emailing them again.

A cool feature that was implemented was a daemon thread function that will stop the sending of emails if you press any key. This threading function is a daemon thread that works in the background of the Python program, and is triggered by a user input of any key which then triggers the setting of a boolean flag. Based on the status of this flag (True or False), the program will stop and exit gracefully. Caveat: This only works in the overseeing mode.

**Second step:&nbsp;** After some time or after you are satisfied with the number of subjects that have signed up for a time slot via the Google Form, the responses will be located in a Google Sheet with the correct format. Run the script again, and instead of inputting a letter i, type in the letter c, which means that you are sending a confirmation email to the subjects.

The script will prompt you for the url to the Google sheet to the blacklist again, to make sure you are not emailing people who have signed up for a time slot, and who are also on the blacklist of emails.

You will need a confirmation email template ([confirmation\_email\_template.html](https://github.com/k-chuang/automate-menial-tasks/blob/master/html/confirmation_email_template.html)), which will generate a confirmation email html body by replacing [DATE] and [TIME] with the associated date and time of a particular participant's appointment. An example is shown in [confirmation\_email.html](https://github.com/k-chuang/automate-menial-tasks/blob/master/html/confirmation_email.html).

The emailer will then start sending emails in oversee mode, similar to the first step.

Again, like in the first step, you can stop the program by pressing any key, which will trigger the daemon thread to properly stop the program.

The person who runs the script should make sure to keep track of what users have been confirmed, by inserting a new column (called Emailed) in the Google Sheet Responses, to make sure they are not sending confirmation emails to people who have already been confirmed. Every batch of emails sent, you should mark an 'x' in each respective row to indicate that the subject has been emailed.

## Conclusion

You are done!

This was a very high level overview of how the script works, but hopefully you guys have learned something from it. I do admit that there is a lot of setting up to do and the script could definitely be more automated. However, this does save a lot of time, effort, and manual labor in scheduling people by automating sending emails to a massive list of subjects.

Again, the GitHub repo for this application is located here: [Python Outlook Emailer](https://github.com/k-chuang/automate-menial-tasks/blob/master/automate-outlook-emailer.py)
