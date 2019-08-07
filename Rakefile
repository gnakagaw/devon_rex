require 'erb'
require 'dotenv/load'

namespace :dockerfile do
  desc 'Generate Dockerfile from a template'
  task :generate do
    path = Pathname(build_context)
    template = ERB.new(File.read(path / 'Dockerfile.erb'))
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

namespace :docker do
  desc 'Run docker build'
  task :build => 'dockerfile:generate' do
    sh "docker build -t #{image_name} -f #{build_context}/Dockerfile ."
  end
end

def image_name
  "sider/devon_rex_#{build_context}:#{tag}"
end

def build_context
  ENV.fetch('BUILD_CONTEXT')
end

def tag
  ENV.fetch('TAG')
end
