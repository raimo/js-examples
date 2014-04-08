require 'typhoeus'
require 'mechanize'

def codepen(github_url)
  pen_attributes = { username: ENV['USERNAME'], pen_username: ENV['USERNAME'], private: true, private_pen: true }
  index = Typhoeus.get(github_url).response_body

  %w(html js css).each do |format|
    entries = index.scan(%r{"(/[^"]+/[^"]+\.#{format})"})
    next if entries.empty?
    url = "https://raw.githubusercontent.com#{entries.first.first}".sub('/blob/', '/')
    pen_attributes[format] = Typhoeus.get(url).response_body rescue nil
  end

  agent = Mechanize.new
  authenticity_token = agent.get('https://codepen.io/login').search('[name=csrf-token]').first.attributes['content'].value
  agent.post('https://codepen.io/login/login', email: ENV['USERNAME'], password: ENV['PASSWORD'], authenticity_token: authenticity_token)
  authenticity_token = agent.get('http://codepen.io/').search('[name=csrf-token]').first.attributes['content'].value
  response_body = agent.post('http://codepen.io/pen/save', {pen: pen_attributes.to_json}, {'X-CSRF-Token' => authenticity_token}).body
  "https://codepen.io/#{ENV['USERNAME']}/pen/#{response_body.match(/slug_hash_private":"([A-Za-z0-9]*)"/)[1]}"
end

task :default do
  remote = `git remote -v`.scan(%r{github.com[:a-z\-/]*}).first
  puts 'Created these codepens for you:'
  `git ls-tree master -d --name-only`.lines.each do |project|
    puts codepen("https://#{remote.sub(':', '/')}/tree/master/#{project}")
  end
end