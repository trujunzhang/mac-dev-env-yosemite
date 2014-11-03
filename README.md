mac-dev-env
===========



Mac Dev Env Setup
	homebrew
	php 5.6
	update mac unix tools
	paths
	git
	ruby
	mysql/mariadb
	zsh
	node
	nginx
	composer
	bower
	bundler
	Grunt
	gulp
	cask - pretty much all your apps
	mackup - keep your app settings in sync. wHAT?! word.
	st3/chrome extensions
	iterm settings



// create a bash script to do all this for you - inspired by
	// http://lapwinglabs.com/blog/hacker-guide-to-setting-up-your-mac
	// https://github.com/IcaliaLabs/kaishi

Run this exactly like this.

system pref
	change name of computer
	mouse/trackpad settings
	hotspots

software updates
	make sure youve updated everything, thought i did, and now it says it hasnt installed yosemite so fuck me right?

xcode dev tools:
	xcode-select --install
		get xcode
		and install dev tools

# xquartz
curl http://xquartz-dl.macosforge.org/SL/XQuartz-2.7.7.dmg -o /tmp/XQuartz.dmg
open /tmp/XQuartz.dmg

homebrew:
	ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
	echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.bash_profile
	brew doctor
	brew update
	brew upgrade
	brew tap homebrew/dupes
	brew tap homebrew/versions
	brew tap homebrew/homebrew-php # theres also: brew tap homebrew/php
	brew install php56
	brew install imagemagick


// update the unix tools you already have on your mac.
# Install GNU core utilities (those that come with OS X are outdated)
brew install coreutils

# Install GNU `find`, `locate`, `updatedb`, and `xargs`, g-prefixed
brew install findutils

# Install Bash 4
brew install bash

# Install more recent versions of some OS X tools
brew install homebrew/dupes/grep

// You'll also need to update the $PATH in your ~/.bash_profile in order to use these tools over their Mac counterparts:
echo export PATH="$(brew --prefix coreutils)/libexec/gnubin:$PATH" >> ~/.bash_profile

brew cleanup

ZSH - https://github.com/robbyrussell/oh-my-zsh
	brew install zsh
	sudo mv /etc/zshenv /etc/zprofile
	cat /etc/shells | grep zsh || which zsh | sudo tee -a /etc/shells
	chsh -s $(which zsh)


# Setup PHP CLI binary

# If you use Bash
echo 'export PATH="$(brew --prefix homebrew/php/php56)/bin:$PATH"' >> ~/.bash_profile && . ~/.bash_profile

# If you use ZSH
echo 'export PATH="$(brew --prefix homebrew/php/php56)/bin:$PATH"' >> ~/.zshrc && . ~/.zshrc


# Setup auto start
mkdir -p ~/Library/LaunchAgents
cp /usr/local/Cellar/php56/5.6.2/homebrew.mxcl.php56.plist ~/Library/LaunchAgents/

# start PHP-FPM
launchctl load -w ~/Library/LaunchAgents/homebrew.mxcl.php56.plist

# Make sure PHP-FPM is listening on port 9000:
lsof -Pni4 | grep LISTEN | grep php


mysql
	brew install mysql
	// mysql.server start // not sure if needed


MariaDB
	brew unlink mysql
	brew info mariadb # Verify MariaDB Version in Homebrew Repo
	brew install mariadb # Install MariaDB

	# mysql setup auto start and start the database
	ln -sfv /usr/local/opt/mysql/*.plist ~/Library/LaunchAgents
	launchctl load ~/Library/LaunchAgents/homebrew.mxcl.mysql.plist

	# Run the Database Installer
	unset TMPDIR
	cd /usr/local/Cellar/mariadb/{VERSION}
	mysql_install_db

	mysql.server start # Start MariaDB
	mysql_secure_installation # Secure the Installation
	mysql -u root -p # Connect to MariaDB



Git
	brew install git

	# Write settings to ~/.gitconfig
	git config --global user.name '{YOUR NAME}'
	git config --global user.email {YOUR EMAIL}

	# a global git ignore file:
	git config --global core.excludesfile '~/.gitignore'
	echo '.DS_Store' >> ~/.gitignore

	# use keychain for storing passwords
	git config --global credential.helper osxkeychain

	# you might not see colors without this
	git config --global color.ui true

	# more useful settings: https://github.com/glebm/dotfiles/blob/master/.gitconfig

	# ssh keys - probably can skip this since github app auto adds it for you which is nice
	ls -al ~/.ssh # Lists the files in your .ssh directory, if they exist
	ssh-keygen -t rsa -C "your_email@example.com" # Creates a new ssh key, using the provided email as a label
	eval "$(ssh-agent -s)" # start the ssh-agent in the background
	ssh-add ~/.ssh/id_rsa
	pbcopy < ~/.ssh/id_rsa.pub # Copies the contents of the id_rsa.pub file to your clipboard to paste in github or w/e


RVM
	curl -L https://get.rvm.io | bash -s stable --ruby
	brew update && brew upgrade
	rvm reinstall 2.1.3 --disable-binary


Node
	brew update
	brew install node



composer
	brew update
	brew install composer
bower
	npm install -g bower
bundler
	gem install bundler
		guard
Grunt
	npm install -g grunt-cli
gulp
	npm install --global gulp



http://akrabat.com/php/setting-up-php-mysql-on-os-x-yosemite/



Cask
	brew install caskroom/cask/brew-cask
		// If you want to install beta versions of things like Chrome Canary or Sublime Text 3,
		brew tap caskroom/versions
		brew cask install google-chrome # per app
	brew update && brew upgrade brew-cask && brew cleanup && brew cask cleanup # when done

	// fonts
	brew tap caskroom/fonts

	// apps
	bash script to install apps -> apps.sh
		chmod u+x apps.sh # make it executable
		./apps.sh # run it

	chrome
		edit this cookie
		page speed insights
		yslow
		sway keys
		adblocker
		eye dropper
		panda
		onetab
		hola
		dewey bookmarks
		Chrome launcher app

	iterm
		cd ~
		curl -O https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.bash_profile
		curl -O https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.bash_prompt
		curl -O https://raw.githubusercontent.com/nicolashery/mac-dev-setup/master/.aliases

	st3
		install package installer
			ctrl+` # opens console in sublie
				import urllib.request,os,hashlib; h = '7183a2d3e96f11eeadd761d777e62404' + 'e330c659d4bb41d3bdf022e94cab3cd0'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)

			theme-default
			brogrammer
			sidebar-enhnacemnets
			sidebar-folders
			tabs-extra
			alignment
			codekit
			emmet
			angularjs
			sass
			scss
			docblocker
			editorconfig
			html-css-js-prettify
			js hint
			move tab
			origami
			markdown
			movetab
			unsplash
			wordpress
			TrailingSpaces
			sublimecodeintel
			jekyll



# Install Mackup
brew install mackup

# Launch it and back up your files
mackup backup