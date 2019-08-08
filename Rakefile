require 'erb'
require 'dotenv/load'

# TODO: Add the rest of contexts
BUILD_CONTEXTS = %w[base go java npm].freeze

namespace :dockerfile do
  desc 'Generate Dockerfile from a template'
  task :generate do
    BUILD_CONTEXTS.each do |build_context|
      path = Pathname(build_context)
      template = ERB.new((path / 'Dockerfile.erb').read)
      result = <<~EOD
        # !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
        # NOTE: DO *NOT* EDIT THIS FILE.  IT IS GENERATED.
        # PLEASE UPDATE Dockerfile.erb INSTEAD OF THIS FILE
        # !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
        #{template.result.chomp}
      EOD
      File.write(path / 'Dockerfile', result)
    end
  end

  desc 'Verify Dockerfile is committed'
  task :verify do
    begin
      sh 'git diff --exit-code'
    rescue
      STDERR.puts 'Run `bundle exec rake dockerfile:generate` and include the changes in commit'
      exit(1)
    end
  end
end

namespace :docker do
  desc 'Run docker build'
  task :build => 'dockerfile:generate' do
    sh "docker build -t #{image_name} -f #{build_context}/Dockerfile ."
  end

  desc 'Run docker run without command. This task expects each image to show versions'
  task :run do
    sh "docker run #{image_name}"
  end
end

def image_name
  "sider/devon_rex_#{build_context}:#{tag}"
end

def build_context
  ENV.fetch('BUILD_CONTEXT')
end

def tag
  ENV.fetch('TAG').tap { |value| raise 'Environment variable `TAG` must not be an empty string.' unless value.length > 0 }
end
