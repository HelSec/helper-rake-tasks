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

## Send emails

Run rake task and give csv filename as parameter

    bundle exec rake emails:send_payment_info[file/path.csv,"Email Address","Reference #"]
