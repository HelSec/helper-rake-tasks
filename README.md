# Payment info mailer

Send payment info to HelSec's members.

Reads info email and reference number from csv file and then sends email for the member.

# Prerequisites

Project requires ruby.

# Installation

Install gems

    bundle install

# Usage

## Environment settings

Create `.env.local` file and add following settings for the smtp server.

    REPLY_TO=reply@example.com
    EMAIL_FROM=sender@example.com
    EMAIL_GREETINGS=My Name / HelSec Ry
    MEMBER_FEE=20€
    ACCOUNT_NUMBER=FI12 3456 7890 1234
    ASSOCIATION=HelSec ry
    SUBJECT=HelSec Ry. membership
    SMTP_SERVER=smtp.example.com
    SMTP_PORT=587
    SMTP_USERNAME=username
    SMTP_PASSWORD=password

## Send emails

Run rake task and give csv filename as parameter

    bundle exec rake emails:send_payment_info[file/path.csv,"Email Address","Reference #"]
