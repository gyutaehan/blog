---
layout: post
comments: true
title: Mac에서 CocoaPods설치중 에러해결
date: 2021-01-01 10:18:00
categories: dev
---

아이폰과 Watson연결 공부를 하려고 [CocoaPods](https://guides.cocoapods.org/using/getting-started.html#getting-started)를 설치하려고 하던 중, 

다음과 같은 설치 에러가 발생했다.

```bash
$ sudo gem install cocoapods                                                                                                                 
Building native extensions. This could take a while...
ERROR:  Error installing cocoapods:
	ERROR: Failed to build gem native extension.

    current directory: /Library/Ruby/Gems/2.6.0/gems/ffi-1.14.2/ext/ffi_c
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/bin/ruby -I /Library/Ruby/Site/2.6.0 -r ./siteconf20210101-11931-rfm5dv.rb extconf.rb
checking for ffi.h... *** extconf.rb failed ***
Could not create Makefile due to some reason, probably lack of necessary
libraries and/or headers.  Check the mkmf.log file for more details.  You may
need configuration options.

Provided configuration options:
	--with-opt-dir
	--without-opt-dir
	--with-opt-include
	--without-opt-include=${opt-dir}/include
	--with-opt-lib
	--without-opt-lib=${opt-dir}/lib
	--with-make-prog
	--without-make-prog
	--srcdir=.
	--curdir
	--ruby=/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/bin/$(RUBY_BASE_NAME)
	--with-ffi_c-dir
	--without-ffi_c-dir
	--with-ffi_c-include
	--without-ffi_c-include=${ffi_c-dir}/include
	--with-ffi_c-lib
	--without-ffi_c-lib=${ffi_c-dir}/lib
	--enable-system-libffi
	--disable-system-libffi
	--with-libffi-config
	--without-libffi-config
	--with-pkg-config
	--without-pkg-config
/System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:467:in `try_do': The compiler failed to generate an executable file. (RuntimeError)
You have to install development tools first.
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:585:in `block in try_compile'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:534:in `with_werror'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:585:in `try_compile'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:1109:in `block in have_header'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:959:in `block in checking_for'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:361:in `block (2 levels) in postpone'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:331:in `open'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:361:in `block in postpone'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:331:in `open'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:357:in `postpone'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:958:in `checking_for'
	from /System/Library/Frameworks/Ruby.framework/Versions/2.6/usr/lib/ruby/2.6.0/mkmf.rb:1108:in `have_header'
	from extconf.rb:10:in `system_libffi_usable?'
	from extconf.rb:42:in `<main>'

To see why this extension failed to compile, please check the mkmf.log which can be found here:

  /Library/Ruby/Gems/2.6.0/extensions/universal-darwin-20/2.6.0/ffi-1.14.2/mkmf.log

extconf failed, exit code 1

Gem files will remain installed in /Library/Ruby/Gems/2.6.0/gems/ffi-1.14.2 for inspection.
Results logged to /Library/Ruby/Gems/2.6.0/extensions/universal-darwin-20/2.6.0/ffi-1.14.2/gem_make.out


```

에러메시지를 보니 개발자 도구(block in try_compile 등)가 없어서 에러가 발생함.
구글링 하다가 [stackoverflow](https://stackoverflow.com/questions/65459161/how-do-i-fix-the-error-failed-to-build-gem-native-extension-error-when-insta)에서 시킨대로 Xcode-select를 인스톨하고 실행해보자

```bash
$ xcode-select --install                                                                                                               
xcode-select: error: command line tools are already installed, use "Software Update" to install updates
```
? 근데 이미 설치가 되어있다고 나왔다..
여기서 다시 한 번 실행해도
````bash
$ sudo gem install cocoapods
````
역시 같은 에러가 발생했다.

찾고 있다가 또 다른 [링크](https://stackoverflow.com/questions/20939568/error-error-installing-cocoapods-error-failed-to-build-gem-native-extension)를 발견하였다. homebrew로 설치하라는 내용이었다

```bash
$ brew cleanup -d -v                                                                                                                    ...
Pruned 0 symbolic links and 6 directories from /usr/local
```

CocoaPods 인스톨을 실행하면

```bash
$ brew install cocoapods                                                                                                                     
Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> Updated Formulae
Updated 3 formulae.
```

오.. 된다..

아래와 같이, CocoaPods을 실행해보면 설치된 것을 확인 할 수 있다.

```bash
$ pod --version                                                                                                                              
1.10.0
```