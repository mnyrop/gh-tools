require 'csv'
require 'dotenv'
require 'fileutils'
require 'json'
require 'octokit'

Dotenv.load

REPO              = 'nyu-dss/keywords-data'
ISSUES_FILE       = 'results/issues.csv'
COMMENTS_FILE     = 'results/comments.csv'
MERGED_FILE_JSON  = 'results/issues-with-comments.json'

def write_to_csv(file, data)
  dir = File.dirname file
  FileUtils.mkdir_p dir

  CSV.open(file, 'wb') do |csv|
    csv << data.first.keys
    data.each do |hash|
      csv << hash.values
    end
  end
  puts "\nWriting results to #{file}\n"
end

def write_to_json(file, data)
  dir = File.dirname file
  FileUtils.mkdir_p dir

  File.open(file,"w") do |f|
    f.write JSON.pretty_generate(data)
  end
  puts "\nWriting results to #{file}\n"
end

namespace :get do
  desc 'write issues to csv'
  task :issues do
    client = Octokit::Client.new access_token: ENV['TOKEN']
    client.auto_paginate = true

    issues = client.list_issues(REPO, state: 'all').reverse
    hashes = issues.each.map do |i|
      {
        url: i.url.to_s,
        title: i.title.to_s,
        user: i.user.login.to_s,
        number: i.number.to_s,
        state: i.state.to_s,
        created_at: i.created_at.to_s,
        updated_at: i.updated_at.to_s,
        closed_at: i.closed_at.to_s,
        body: i.body.to_s
      }
    end
    write_to_csv ISSUES_FILE, hashes
  end

  desc 'write comments to csv'
  task :comments do
    client = Octokit::Client.new access_token: ENV['TOKEN']
    client.auto_paginate = true

    hashes = client.issues_comments(REPO).each.map do |c|
      {
        url: c.url.to_s,
        issue_url: c.issue_url.to_s,
        created_at: c.created_at.to_s,
        user: c.user.login.to_s,
        body: c.body.to_s
      }
    end
    write_to_csv COMMENTS_FILE, hashes
  end
end

namespace :merge do
  desc 'merge comments and issues for JIRA import'
  task :results do
    comments = CSV.open(COMMENTS_FILE, headers: :first_row).map(&:to_h)
    issues = CSV.open(ISSUES_FILE, headers: :first_row).map(&:to_h)

    issues.each do |issue|
      related = comments.find_all { |c| c['issue_url'] == issue['url'] }
      related.each_with_index do |c,i|
        # "each comment column value needs to have
        # the timestamp,
        # username (netid would work),
        # and the actual comment, separated by semi colons."

        issue["comment-#{i}"] = "#{c['created_at']};#{c['user']};#{c['body']}"
      end
    end

    write_to_json MERGED_FILE_JSON, issues
  end
end
