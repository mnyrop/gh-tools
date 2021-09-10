require 'csv'
require 'dotenv'
require 'json'
require 'octokit'

Dotenv.load

namespace :get do
  desc 'get issues and comments'
  task :issues do
    client = Octokit::Client.new access_token: ENV['TOKEN']
    client.auto_paginate = true
    issues = client.list_issues ENV['REPO'], state: 'all'
    issues.each do |i| # iterate through issues
      
      # temp inspect
      puts i.title
      puts i.user.login
      puts i.number
      puts i.state
      puts i.milestone
      puts i.comments
      puts i.created_at
      puts i.updated_at
      puts i.closed_at
      puts i.body
      
      if i.comments >= 0
        puts "has comments!" 
        comments = Octokit.issue_comments("octokit/octokit.rb", i.number)
        comments.each do |c|
          puts c.inspect
        end
      end
      
      
      # TO DO
      # Add info to hashes and spit out a CSV instead of printing to stdout
    end
  end
end
