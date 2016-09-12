#!/usr/bin/env rake
$LOAD_PATH.push File.expand_path("../lib", __FILE__)
require 'rake/testtask'
require "flipper/version"

# gem install pkg/*.gem
# gem uninstall flipper flipper-ui flipper-redis
desc 'Build gem into the pkg directory'
task :build do
  FileUtils.rm_rf('pkg')
  Dir['*.gemspec'].each do |gemspec|
    system "gem build #{gemspec}"
  end
  FileUtils.mkdir_p('pkg')
  FileUtils.mv(Dir['*.gem'], 'pkg')
end

desc 'Tags version, pushes to remote, and pushes gem'
task :release => :build do
  sh 'git', 'tag', "v#{Flipper::VERSION}"
  sh "git push origin master"
  sh "git push origin v#{Flipper::VERSION}"
  sh "ls pkg/*.gem | xargs -n 1 gem push"
end

require "rspec/core/rake_task"
RSpec::Core::RakeTask.new(:spec) do |t|
  t.rspec_opts = %w(--color)
end

namespace :spec do
  desc "Run specs for UI queue"
  RSpec::Core::RakeTask.new(:ui) do |t|
    t.rspec_opts = %w(--color)
    t.pattern = ["spec/flipper/ui/**/*_spec.rb", "spec/flipper/ui_spec.rb"]
  end
end

Rake::TestTask.new do |t|
  t.libs = ['lib', 'test']
  t.pattern = "test/**/*_test.rb"
end

Rake::TestTask.new(:shared_test) do |t|
  t.libs = ['lib', 'test']
  t.pattern = "lib/flipper/shared/test/**_test.rb"
end

task :default => [:spec, :test, :shared_test]
