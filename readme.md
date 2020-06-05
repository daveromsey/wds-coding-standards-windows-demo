# Goal

I wanted to get the new WDS Coding Standards setup working on Windows via the command line and in VS Code. This project contains an example setup and documentation I used to get everything working.

# Description

This is a demo using the new project-specific WDS Coding Standards setup. NPM and Composer scripts have been modified so that they work on Windows (via the Git Bash terminal). I have not tested this on Mac/Linux, so I'm not sure if the changes I've made will break things over there. I'm hoping that we can get to a point where the setup is platform agnostic.

For demonstration purposes, a placeholder plugin has been added to `wp-content/plugins/dr-coding-standards-test`. It doesn't do anything, but it does contain PHP, JS, CSS, and Sass files that will trigger linting errors.

Instructions for setting up VS Code to run real time linting have also been included in this file.

# Instructions

- Create a fresh WordPress install using Local by Flywheel, for example.
- Rename the `wp-content` directory to `wp-content-backup` temporarily.
- Clone this repo to a new `wp-content` directroy.
- Copy any of the themes or plugins you'd like to keep from `wp-content-backup` back into the new `wp-content` directory.
- Navigate to `wp-content` and run `npm install` and `composer install` to install dependencies.
- Run `npm run lint` to perform all linting tasks. Linting errors will be generated for the example PHP, JS, CSS, and Sass files included in the demo setup.


# NPM Scripts Changes

The following changes have been made to NPM scripts inside of `package.json`:

- Added `lint:scripts`
- Updated `lint:all`: Now it also runs `lint:scripts`
- Updated `lint:js` and `lint:css`: When linting errors are detected, output message stating that a linting error has occured and suppress general NPM error messages. e.g. `npm ERR! code ELIFECYCLE`
- Updated `lint:css`: Remove quotes from file pattern path. Fixes files not being found for linting.
- Updated `lint:scss`: Remove quotes from file pattern path. Fixes files not being found for linting.
- Project-specific: Update plugin-specific paths to `./plugins/dr-coding-standards-test` for demonstration.


# Composer Scripts Changes

The following changes have been made to Composer scripts inside of `composer.json`:

- Added `debug:phpcs` to help with debugging PHPCS issues. Shows which PHPCS will be used and what sniffs are loaded. This is handy because [Composer modifies the PATH environment variable](https://getcomposer.org/doc/articles/scripts.md#writing-custom-commands)*1 and therefore it could load a different version of PHPCS than when calling it directly from the command line.
- Updated `lint:php-compat` and `lint:phpcs`: Remove the path preceding the `composer` command as it causes a fatal error in Windows. See *1 below.
- Updated `lint:php-compat` and `lint:phpcs`: Use `memory_limit-1` to fix out of memory errors.
- Updated `lint:phpcs`: Append ` || true || exit` to command to fix issue where chained commands would fail due to output being generated when linting errors occur.
		
*1
> Note: Before executing scripts, Composer's bin-dir is temporarily pushed on top of the PATH environment variable so that binaries of dependencies are easily accessible. 


# VS Code Setup

Tested using fresh install of VS Code in standalone mode. Linting will be applied automatically to PHP, JS, CSS, and Sass files.

## Required VS Code extensions

- [ESLint (Dirk Baeumer)](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
- [phpcs (Ioannis Kappas)](https://marketplace.visualstudio.com/items?itemName=ikappas.phpcs)
- [stylelint (stylelint)](https://marketplace.visualstudio.com/items?itemName=stylelint.vscode-stylelint)
- [stylelint (stylelint)](https://marketplace.visualstudio.com/items?itemName=stylelint.vscode-stylelint)


## VS Code `settings.json`

```
	{
		"phpcs.enable": true,
		// phpcs.executablePath is relative to the workspace directory. i.e. D:/dev/local-sites/wdscodingstandards/
		"phpcs.executablePath": "app/public/wp-content/vendor/squizlabs/php_codesniffer/bin/phpcs",   
		 // "phpcs.trace.server": "messages", // For debugging PHPCS issues. See Output Console -> PHP Code Sniffer

		"eslint.enable": true,
		"eslint.packageManager": "npm",
		"eslint.workingDirectories": [
			{
					"mode": "auto"
			}
		],
		"eslint.options": {
			"maxWarnings": "500",
			"ignorePattern": [
				"/node_modules/*", // Optional: Ignore files in node_modules dir.
				"*.min.js"
			]
		},
		// "eslint.debug": true, // For debugging ESLint issues.  See Output Console -> ESLint

		"stylelint.enable": true,
		"css.validate": false,
		"scss.validate": false,
		"less.validate": false
	}
```
