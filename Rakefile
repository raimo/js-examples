require 'typhoeus'
require 'mechanize'

def codepen(github_url)
  assets = {}
  index = Typhoeus.get(github_url).response_body

  %w(html js css).each do |format|
    entries = index.scan(%r{"(/[^"]+/[^"]+\.#{format})"})
    next if entries.empty?
    url = "https://raw.githubusercontent.com#{entries.first.first}".sub('/blob/', '/')
    assets[format] = Typhoeus.get(url).response_body rescue nil
  end

  "https://codepen.io/anon/pen/#{Mechanize.new.post('http://codepen.io/pen/save', pen: assets.to_json).body.match(/slug_hash":"([A-Za-z0-9]*)"/)[1]}"
end

task :default do
  remote = `git remote -v`.scan(%r{github.com[:a-z\-/]*}).first
  puts 'Created these codepens for you:'
  `git ls-tree master -d --name-only`.lines.each do |project|
    puts codepen("https://#{remote.sub(':', '/')}/tree/master/#{project}")
  end
end