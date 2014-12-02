#!/usr/bin/env rake

require 'github_api'
require 'xmlsimple'
require 'pry'

# Get a 'personal access token' from here: https://github.com/settings/applications
GH_TOKEN = "PERSONAL_ACCESS_TOKEN"
GH_ORGANISATION = "ORGANISATION_NAME"

desc "Upload and create books"
task :upload do
  github = Github.new :oauth_token => GH_TOKEN

  to_upload = Dir.glob('pending/*.xml')
  puts "Working with #{to_upload.size} books"

  to_upload.each do |book|
    xml_name = File.basename(book)
    repo_name = xml_name.gsub('.xml', '')

    # Could set the description of the repository here also
    github.repos.create(:name => repo_name, :homepage => 'http://ox.ac.uk')

    # Upload book
    github.repos.contents.create GH_ORGANISATION, repo_name, xml_name,
      :path => xml_name,
      :content => File.open(book, 'r').read,
      :message => "Initial import of #{repo_name}"

    # Read XML
    data = XmlSimple.xml_in(File.open(book, 'r'))

    title = data['teiHeader'].first['fileDesc'].first['titleStmt'].first['title'].first
    author = data['teiHeader'].first['fileDesc'].first['titleStmt'].first['author'].first

    # Build up string for README contents
    content = ""
    content << "**Title:** #{title}  \n"
    content << "**Author:** #{author}  \n"
    content << "**License:** TODO - should probably set a license here (or write an additional file)"

    # Upload README.md
    github.repos.contents.create GH_ORGANISATION, repo_name, 'README.md',
      :path => "README.md",
      :content => content,
      :message => "Create README.md"

    # Finally move file to 'complete'
    File.rename(book, "complete/#{xml_name}")

    puts "Uploaded #{book}"
  end
end
