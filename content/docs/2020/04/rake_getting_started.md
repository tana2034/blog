---
title: "[rake]初めてのrake"
weight: 1
# bookFlatSection: false
# bookToc: true
# bookHidden: false
# bookCollapseSection: false
# bookComments: true
---

# [rake]初めてのrake

[RAKE – Ruby Make](https://ruby.github.io/rake/)について調べました

## Rakeとは

Rakeとは、MakeのようなことをするためのRubyの実装、
Ruby + makeでrake

### とにかく使ってみる

[simple example](https://github.com/ruby/rake#label-Simple+Example)を実行してみる

- `Rakefile`を作成

```Ruby
task default: %w[test]

task :test do
  ruby "test/unittest.rb"
end
```

下記コマンドを実行すると…unittest.rbが実行される

```bash
rake
# ruby test/unittest.rb

rake test
# ruby test/unittest.rb 
# これも同じ結果になる
```

### rake taskを増やしたい

```ruby
task :test do
...
end

task :md_to_html do
  md_file = "new.md"
  html_file = "new.html"
  sh "pandoc -o #{html_file} #{md_file}"
end
```

```bash
rake md_to_html
# pandoc -o new.html new.md
```
追加できた、[色々描けるみたい](https://github.com/ruby/rake/blob/master/doc/rakefile.rdoc#label-Multiple+Definitions)

### file task を追加する

ファイルの作成をするためのタスクは、`task` ~ でなく、`file` ~　で始める。
https://github.com/ruby/rake/blob/master/doc/rakefile.rdoc#label-File+Tasks

```ruby
#Rakefile
file 'foo.txt' do
  touch 'foo.txt'
end
```

```bash
rake foo.txt
# touch foo.txt
rake foo.txt
# 二回目はもうやらない
rm foo.txt
rake foo.txt
# touch foo.txt
```

正直、通常のtaskと何が違うのかよくわからなかった

```ruby
# Rakefile
task 'bar.txt' do
  touch 'bar.txt'
end
```

```bash
rake bar.txt
# touch bar.txt
rake bar.txt
# touch bar.txt
# もう一度実行されるけど、中身をうわがいているわけではなさそうだった
```

### directory taskを追加する

```ruby
# Rakefile
directory "testdata/examples/doc"
```

```bash
rake testdata/examples/doc
# mkdir -p testdata/examples/doc
```

### 並列実行

```ruby
# Rakefile

multitask copy_files: %w[copy_src copy_doc copy_bin] do
  puts "All Copies Complete"
end

task :copy_src do
  sh "cp bar.txt src/bar.txt"
end

task :copy_doc do
  sh "cp bar.txt doc/bar.txt"
end

task :copy_bin do
  sh "cp bar.txt bin/bar.txt"
end
```

```bash
rake copy_files
cp bar.txt bin/bar.txt
cp bar.txt src/bar.txt
cp bar.txt doc/bar.txt
All Copies Complete
```

### taskに引数を渡す

```ruby
# Rakefile
task :name, [:first_name, :last_name] do |t, args|
  puts "First name is #{args.first}"
  puts "Last name is #{args.last}"
end
```

ちなみに、tについて

> The first argument of the block “t” is always bound to the current task object. 


```bash
rake name[aaa,bbb]
# First name is aaa
# Last name is bbb
```

### Accessing Task programatically
https://github.com/ruby/rake/blob/master/doc/rakefile.rdoc#label-Accessing+Task+Programmatically

`Rake::Task[:taskname]`で、他のタスクにアクセスできる

```Ruby
# Rakefile
task :doit do
  puts "DONE"
end

task :dont do
  Rake::Task[:doit].clear
end
```

### Rules

特定のファイルに対するfileタスクがなくとも、ruleを設定しておくことによって、タスクを実行させることができる

```ruby
# Rakefile
rule '.txt' do |task|
  puts 'creating file: %s' % task.name
  touch task.name
end
```

```bash
rake helloworld.txt
# creating file: helloworld.txt
# touch helloworld.txt
```

これうまくいかないけどなんでだろ

```ruby
# Rakefile
rule '.html' => '.md' do |t|
  sh "pandoc -o #{t.name} #{t.source}"
end
```

### Namespaces

db:migrateとかでよく見ている気がする

```ruby
# Rakefile
namespace "main" do
  task :build do
    puts "this is main build"
  end
end

namespace "samples" do
  task :build do
    puts "this is sample build"
  end
end

task build: %w[main:build samples:build]
```

```bash
rake main:build
# this is main build
rake samples:build
# this is sample build
rake build
# this is main build
# this is sample build
```


## 残った疑問

- rubyっぽく見えるけどshellコマンドはどこまで投げられるの
- ファイル分けられないの？全部Rakefile？