require 'json'
require 'pathname'
require 'yaml'

VIRTUAL_MODULES = %w[all]
VIRTUAL_MANIFESTS = Set[*VIRTUAL_MODULES.map { |name| "#{name}/.asimov/module.yaml" }]
MODULE_MANIFESTS = Set[*Dir['**/.asimov/module.yaml'].sort] - VIRTUAL_MANIFESTS

task default: %w[modules:readme]

namespace :modules do
  task count: MODULE_MANIFESTS.to_a do |t|
    puts t.prerequisites.size
  end

  task names: MODULE_MANIFESTS.to_a do |t|
    t.prerequisites.each do |manifest_path|
      manifest = YAML.safe_load(File.read(manifest_path))
      puts manifest['name']
    end
  end

  task packages: MODULE_MANIFESTS.to_a do |t|
    t.prerequisites.each do |manifest_path|
      manifest = YAML.safe_load(File.read(manifest_path))
      puts "asimov-#{manifest['name']}-module"
    end
  end

  task paths: MODULE_MANIFESTS.to_a do |t|
    t.prerequisites.each do |manifest_path|
      puts manifest_path
    end
  end

  task yaml: MODULE_MANIFESTS.to_a do |t|
    t.prerequisites.each.with_index do |manifest_path, index|
      puts if index.positive?
      puts File.read(manifest_path)
    end
  end

  task json: MODULE_MANIFESTS.to_a do |t|
    output = {}
    t.prerequisites.each do |manifest_path|
      manifest = { '@type': 'AsimovModule' }.merge(YAML.safe_load(File.read(manifest_path)))
      output[manifest['name']] = manifest
    end
    puts JSON.pretty_generate(output)
  end

  task jsonl: MODULE_MANIFESTS.to_a do |t|
    t.prerequisites.each do |manifest_path|
      manifest = { '@type': 'AsimovModule' }.merge(YAML.safe_load(File.read(manifest_path)))
      puts JSON.generate(manifest)
    end
  end

  task readme: MODULE_MANIFESTS.to_a do |t|
    puts %w[Name Label Summary Package].join(' | ')
    puts %w[:--- :---- :------ :------].join(' | ').ljust(80, '-')
    t.prerequisites.each do |manifest_path|
      manifest = YAML.safe_load(File.read(manifest_path))
      name_link = "[#{manifest['name']}](https://github.com/asimov-modules/asimov-#{manifest['name']}-module)"
      crate_link = "[🦀](https://crates.io/crates/asimov-#{manifest['name']}-module)"
      puts [name_link, manifest['label'], manifest['summary'], crate_link].join(' | ')
    end
  end

  task pull: MODULE_MANIFESTS.to_a do |t|
    t.prerequisites.each do |manifest_path|
      manifest = YAML.safe_load(File.read(manifest_path))
      module_name = manifest['name']
      Dir.chdir module_name do
        command = "git checkout master && git pull -q"
        puts "cd #{module_name} && #{command}"
        result = system(command)
        abort unless result
      end
    end
  end
end
