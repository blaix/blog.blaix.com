require "colorize"
require "open3"
require "rake"

task :default => :build
task :build do
  sh "bundle exec jekyll build -q"
  Rake::FileList["_site/**/*.html"].each do |file|
    print "#{file}: "
    _, err, status = Open3.capture3("tidy", "-qmi", "-access", "1", "--wrap", "0", file)
    if status.success?
      puts "OK!".green
    else
      abort err.red
    end
  end
end

task :serve => :build do
  sh "bundle exec jekyll serve --skip-initial-build"
end
