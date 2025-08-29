#!/usr/bin/env ruby
# frozen_string_literal: true

# -----------------------------------------------------------
# Password Strength Checker
# -----------------------------------------------------------
# A professional Ruby utility that evaluates password strength
# based on length and character variety.
#
# Features:
# - Checks against multiple security rules
# - Provides human-readable feedback
# - Clear scoring and rating system
# - Handles user input securely (without echoing password)
#
# Author: Rainers
# GitHub: https://github.com/<your-username>
# -----------------------------------------------------------

require "io/console"

# A class responsible for validating passwords
class PasswordStrengthChecker
  RULES = {
    length: { desc: "At least 12 characters", regex: /.{12,}/ },
    upper:  { desc: "At least one uppercase letter", regex: /[A-Z]/ },
    lower:  { desc: "At least one lowercase letter", regex: /[a-z]/ },
    digit:  { desc: "At least one number", regex: /\d/ },
    symbol: { desc: "At least one special character (!@#$%^&*)", regex: /[^A-Za-z0-9]/ }
  }.freeze

  RATING_SCALE = {
    5 => "✅ Strong — Excellent security!",
    3..4 => "⚠️ Medium — Consider improving.",
    0..2 => "❌ Weak — Change immediately!"
  }.freeze

  def self.check(password)
    results = RULES.map do |_, rule|
      { rule: rule[:desc], passed: password.match?(rule[:regex]) }
    end

    score = results.count { |r| r[:passed] }
    rating = rating_for(score)

    { score: score, rating: rating, results: results }
  end

  def self.rating_for(score)
    RATING_SCALE.each { |range, label| return label if range === score }
  end
end

# -----------------------------------------------------------
# Command-line Execution
# -----------------------------------------------------------
if __FILE__ == $PROGRAM_NAME
  puts "🔒 Password Strength Checker"
  puts "-" * 40

  print "Enter password: "
  password = STDIN.noecho(&:gets).chomp
  puts "\n"

  result = PasswordStrengthChecker.check(password)

  puts "Password Strength: #{result[:rating]}"
  puts "-" * 40
  result[:results].each do |r|
    status = r[:passed] ? "✔️" : "❌"
    puts "#{status} #{r[:rule]}"
  end
end
