# Add your own tasks in files placed in lib/tasks ending in .rake,
# for example lib/tasks/capistrano.rake, and they will automatically be available to Rake.

require_relative "config/application"

# This script I found search on internet, just to make easier to create fixtures, specially those that hast attachments

namespace :db do
  desc 'Convert development DB to Rails test fixtures'
  task to_fixtures: :environment do
    TABLES_TO_SKIP = %w[ar_internal_metadata delayed_jobs schema_info schema_migrations].freeze

    begin
      ActiveRecord::Base.establish_connection
      ActiveRecord::Base.connection.tables.each do |table_name|
        next if TABLES_TO_SKIP.include?(table_name)

        conter = '000'
        file_path = "#{Rails.root}/test/fixtures/#{table_name}.yml"
        File.open(file_path, 'w') do |file|
          rows = ActiveRecord::Base.connection.select_all("SELECT * FROM #{table_name}")
          data = rows.each_with_object({}) do |record, hash|
            suffix = record['id'].blank? ? conter.succ! : record['id']
            hash["#{table_name.singularize}_#{suffix}"] = record
          end
          puts "Writing table '#{table_name}' to '#{file_path}'"
          file.write(data.to_yaml)
        end
      end
    ensure
      ActiveRecord::Base.connection.close if ActiveRecord::Base.connection
    end
  end
end

Rails.application.load_tasks
