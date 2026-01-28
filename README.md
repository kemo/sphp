# sphp

A shell script for switching between Brew-installed PHP versions on macOS.

**Version:** 2.0.0

## Usage

```
sphp <version> [options]
```

### Examples

```bash
sphp 8.3              # Switch to PHP 8.3
sphp 8.2 --skip-apache   # Switch PHP without Apache changes
sphp 8.1 --dry-run    # Preview switch to PHP 8.1
sphp --list           # List installed PHP versions
sphp --current        # Show current PHP version
```

### Options

| Option | Description |
|--------|-------------|
| `-s, --skip-apache` | Skip Apache configuration changes |
| `-n, --dry-run` | Show what would be done without making changes |
| `-v, --verbose` | Enable verbose output |
| `-l, --list` | List installed PHP versions and exit |
| `-c, --current` | Show current PHP version and exit |
| `-h, --help` | Show help message |

### Supported PHP Versions

5.6, 7.0, 7.1, 7.2, 7.3, 7.4, 8.0, 8.1, 8.2, 8.3, 8.4, 8.5

## Prerequisites

### Install Xcode Command Line Tools (required)

```bash
xcode-select --install
```

### Install Homebrew

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

### Replace macOS Apache with Homebrew Apache

Stop and disable the built-in Apache:

```bash
sudo apachectl stop
sudo launchctl unload -w /System/Library/LaunchDaemons/org.apache.httpd.plist 2>/dev/null
```

Install Homebrew Apache:

```bash
brew install httpd
```

Test installation by opening http://localhost:8080 in your browser.

Apache control commands:

```bash
brew services start httpd
brew services stop httpd
brew services restart httpd
```

### Install PHP Versions

Add the PHP repository:

```bash
brew tap shivammathur/php
```

Install the PHP versions you need:

```bash
brew install shivammathur/php/php@5.6
brew install shivammathur/php/php@7.0
brew install shivammathur/php/php@7.1
brew install shivammathur/php/php@7.2
brew install shivammathur/php/php@7.3
brew install shivammathur/php/php@7.4
brew install shivammathur/php/php@8.0
brew install shivammathur/php/php@8.1
brew install shivammathur/php/php@8.2
brew install shivammathur/php/php@8.3
brew install shivammathur/php/php@8.4
brew install shivammathur/php/php@8.5
```

> **Note:** PHP versions 5.6-7.2 are deprecated and may cause errors, but are useful for legacy projects.

### PHP Configuration Files

PHP ini files are located at `$(brew --prefix)/etc/php/<version>/php.ini`:

**Apple Silicon (M1/M2/M3):** `/opt/homebrew/etc/php/<version>/php.ini`

**Intel Macs:** `/usr/local/etc/php/<version>/php.ini`

## Apache PHP Setup

Add PHP modules to `httpd.conf` after `LoadModule rewrite_module lib/httpd/modules/mod_rewrite.so`.

The script will automatically manage these entries, but for initial setup:

### Apple Silicon (M1/M2/M3)

```apache
#LoadModule php5_module /opt/homebrew/opt/php@5.6/lib/httpd/modules/libphp5.so
#LoadModule php7_module /opt/homebrew/opt/php@7.0/lib/httpd/modules/libphp7.so
#LoadModule php7_module /opt/homebrew/opt/php@7.1/lib/httpd/modules/libphp7.so
#LoadModule php7_module /opt/homebrew/opt/php@7.2/lib/httpd/modules/libphp7.so
#LoadModule php7_module /opt/homebrew/opt/php@7.3/lib/httpd/modules/libphp7.so
#LoadModule php7_module /opt/homebrew/opt/php@7.4/lib/httpd/modules/libphp7.so
#LoadModule php_module /opt/homebrew/opt/php@8.0/lib/httpd/modules/libphp.so
#LoadModule php_module /opt/homebrew/opt/php@8.1/lib/httpd/modules/libphp.so
#LoadModule php_module /opt/homebrew/opt/php@8.2/lib/httpd/modules/libphp.so
LoadModule php_module /opt/homebrew/opt/php@8.3/lib/httpd/modules/libphp.so
#LoadModule php_module /opt/homebrew/opt/php@8.4/lib/httpd/modules/libphp.so
#LoadModule php_module /opt/homebrew/opt/php@8.5/lib/httpd/modules/libphp.so
```

### Intel Macs

```apache
#LoadModule php5_module /usr/local/opt/php@5.6/lib/httpd/modules/libphp5.so
#LoadModule php7_module /usr/local/opt/php@7.0/lib/httpd/modules/libphp7.so
#LoadModule php7_module /usr/local/opt/php@7.1/lib/httpd/modules/libphp7.so
#LoadModule php7_module /usr/local/opt/php@7.2/lib/httpd/modules/libphp7.so
#LoadModule php7_module /usr/local/opt/php@7.3/lib/httpd/modules/libphp7.so
#LoadModule php7_module /usr/local/opt/php@7.4/lib/httpd/modules/libphp7.so
#LoadModule php_module /usr/local/opt/php@8.0/lib/httpd/modules/libphp.so
#LoadModule php_module /usr/local/opt/php@8.1/lib/httpd/modules/libphp.so
#LoadModule php_module /usr/local/opt/php@8.2/lib/httpd/modules/libphp.so
LoadModule php_module /usr/local/opt/php@8.3/lib/httpd/modules/libphp.so
#LoadModule php_module /usr/local/opt/php@8.4/lib/httpd/modules/libphp.so
#LoadModule php_module /usr/local/opt/php@8.5/lib/httpd/modules/libphp.so
```

### Configure DirectoryIndex

Find this block in `httpd.conf`:

```apache
<IfModule dir_module>
    DirectoryIndex index.html
</IfModule>
```

Replace it with:

```apache
<IfModule dir_module>
    DirectoryIndex index.php index.html
</IfModule>

<FilesMatch \.php$>
    SetHandler application/x-httpd-php
</FilesMatch>
```

## Installation

Download and install the script to your Homebrew bin directory:

```bash
curl -L https://raw.githubusercontent.com/rhukster/sphp.sh/main/sphp > "$(brew --prefix)/bin/sphp"
chmod +x "$(brew --prefix)/bin/sphp"
```

## Testing

Create a test PHP file in Apache's document root:

```bash
printf "<?php\nphpinfo();\n?>" > "$(brew --prefix)/var/www/info.php"
```

Open http://localhost:8080/info.php in your browser. You should see a phpinfo() page.

**Test version switching:**

```bash
sphp 8.2
```

Refresh the phpinfo page (Cmd-R) to verify the version changed.

## Credits

- Original creator: Phil Cook
- Modified by: Andy Miller
- M1 Mac support: Vadym Khomakha

## License

MIT License - see script header for details.
