# frozen_string_literal: true
require 'dotenv/load'
require "csv"
require "mail"
Dotenv.load('.env.local')

Mail.defaults do
  delivery_method :smtp,
    :address    => ENV.fetch("SMTP_SERVER"),
    :port       => ENV.fetch("SMTP_PORT"),
    :user_name  => ENV.fetch("SMTP_USERNAME"),
    :password   => ENV.fetch("SMTP_PASSWORD")
end


namespace :emails do
  desc "Send payment info to members"
  task :send_payment_info, [:filename, :email_column, :reference_column] do |t, args|
    filename = args.fetch(:filename)
    email_column = args.fetch(:email_column)
    reference_column = args.fetch(:reference_column)

    association = ENV.fetch("ASSOCIATION")
    iban = ENV.fetch("ACCOUNT_NUMBER")
    amount = ENV.fetch("MEMBER_FEE")
    greetings = ENV.fetch("EMAIL_GREETINGS")
    email_from = ENV.fetch("EMAIL_FROM")
    reply_to = ENV.fetch("REPLY_TO")
    subject = ENV.fetch("SUBJECT")

    email_body = File.read('emails/payment_info.txt')

    CSV.parse(File.read(filename), headers: true) do |row|
      email_address = row[email_column]
      reference = row[reference_column]

      content = email_body % [
        association,
        association,
        iban,
        amount,
        reference,
        greetings
      ]

      email = Mail.new do
        from                email_from
        reply_to            reply_to
        bcc                 reply_to
        to                  email_address
        subject             subject
        body                content
      end

      begin
        email.deliver!
        puts "Email sent to: #{email_address}"
      rescue => e
        puts "Failed to send email to: #{email_address}"
        puts "Reason: #{e.inspect}"
      end
    end
  end
end
