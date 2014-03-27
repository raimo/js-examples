require 'typhoeus'
require 'mechanize'

def codepen(github_url)
  assets = {}
  index = Typhoeus.get(github_url).response_body

  %w(html js css).each do |format|
    url = "https://raw.githubusercontent.com#{index.scan(%r{"(/[^"]+/[^"]+\.#{format})"}).first.first}".sub('/blob/', '/')
    assets[format] = Typhoeus.get(url).response_body rescue nil
  end

  "https://codepen.io/anon/pen/#{Mechanize.new.post('http://codepen.io/pen/save', pen: assets.to_json).body.match(/slug_hash":"([A-Za-z0-9]*)"/)[1]}"
end

task :default do
  %w(https://github.com/raimo/js-examples/tree/master/cat).each do |url|
    puts codepen(url)
  end
end