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
  task :send_payment_info, [:filename, :email_column, :reference_column, :membernumber_column] do |t, args|
    filename = args.fetch(:filename)
    email_column = args.fetch(:email_column)
    reference_column = args.fetch(:reference_column)
    membernumber_column = args.fetch(:membernumber_column)

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
      membernumber = row[membernumber_column].to_i

      content = email_body % [
        association,
        association,
        iban,
        amount,
        reference,
        membernumber,
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
        sleep 1
      rescue => e
        puts "Failed to send email to: #{email_address}"
        puts "Reason: #{e.inspect}"
      end
    end
  end

  desc "Send confirmation/reminder email"
  task :confirmation_mail, [:filename, :email_column, :membernumber_column, :payment_done_column,:confirmation_mail_sent_column,:reference_column] do |t, args|
    filename = args.fetch(:filename)
    email_column = args.fetch(:email_column)
    membernumber_column = args.fetch(:membernumber_column)
    payment_done_column = args.fetch(:payment_done_column)
    confirmation_mail_sent_column = args.fetch(:confirmation_mail_sent_column)
    reference_column = args.fetch(:reference_column)

    greetings = ENV.fetch("EMAIL_GREETINGS")
    email_from = ENV.fetch("EMAIL_FROM")
    reply_to = ENV.fetch("REPLY_TO")
    subject = ENV.fetch("SUBJECT")
    iban = ENV.fetch("ACCOUNT_NUMBER")
    amount = ENV.fetch("MEMBER_FEE")
    association = ENV.fetch("ASSOCIATION")
    type = ""

    email_body = File.read('emails/membership_confirmation.txt')

    CSV.parse(File.read(filename), headers: true) do |row|
      email_address = row[email_column]
      membernumber = row[membernumber_column].to_i
      payment_done = (row[payment_done_column] || "").strip.downcase
      confirmation_mail_sent = (row[confirmation_mail_sent_column] || "").strip.downcase
      reference = row[reference_column]

      next if "x" == confirmation_mail_sent

      additional_content = ""
      additional_content += "As you are one of the first 100 members, you will get a special members-only sticker. You can get the sticker from meetups by talking to whois. If you are eager to to wait, you can contact us and pick up the sticker from Keilaranta 15. \r\n\r\n" if membernumber < 104
      if "x" == payment_done
        additional_content += "We have got your payment so you are all set."
        type = "confirmed"
      else
        type = "reminder"
        additional_content += "We haven't got your payment yet. "
        if membernumber < 104
          additional_content += "Please do the payment in reasonable time. Otherwise we might consider to move you outside of the first 100 member and your sticker will get someone else who has already paid their member fee."
        else
          additional_content += "Please do the payment in reasonable time. If you have just got the payment info within last few weeks, don't worry, this is just an semi-automated reminder :)"
        end

        additional_content += "\r\n\r\nHere's the payment info:\r\n" +
                              "Receiver: #{association}\r\n" +
                              "IBAN: #{iban}\r\n" +
                              "Amount: #{amount}\r\n" +
                              "Reference: #{reference}"

      end

      content = email_body % [
        membernumber,
        additional_content,
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
        puts "Email[#{type}] sent to: #{email_address}"
        sleep 1
      rescue => e
        puts "Failed to send email to: #{email_address}"
        puts "Reason: #{e.inspect}"
      end
    end
  end
end
