---
layout:     page
menu-title: BEM
title:      Examples > BEM
categories: [examples]
---

On a page there is a sidebar on the left. Inside a sidebar there are multiple sideboxes, and one of them is LoginBox. LoginBox consists of 2 inputs and submit button.

![Example Page](/examples/example_page.png)

```html
<html>
	<head></head>
	<body>
		<div class="b-header">
			Language:
			<select name="b-header__language">
				<option value="1">English</option>
				<option value="2">Russian</option>
			</select>
			Currency:
			<select name="b-header__curr-iso">
				<option value="USD">USD</option>
				<option value="EUR">EUR</option>
			</select>
		</div>
		<div class="b-sidebar">
			<div class="b-login_location_sidebar">
				<div class="b-login__error-msg"></div>
				E-mail or Username: <input type="text" class="b-login__input-username" value=""/><br/>
				Password: <input type="password" class="b-login__input-password_color_red" value=""/><br/>
				<input type="submit" class="b-login__btn-login" value="Login"/>
			</div>
		</div>
	</body>
</html>
```

## Usage

The Page Factory needs to be created only once per Mink session.

<p class="message">
    Do not use <code>getPage</code> of the Page Factory which is currently not fully implemented.
</p>

### Creating Page Factory

No configuration is required to get started.

```php
<?php
use Behat\Mink\Driver\Selenium2Driver;
use Behat\Mink\Session;
use QATools\QATools\BEM\BEMPageFactory;

$session = new Session(new Selenium2Driver());
$page_factory = new BEMPageFactory($session);

$home_page = new HomePage($page_factory);
$home_page->open();
$home_page->examplePageMethod();
```

### Creating Page Factory with a Config

Providing a Config and specifying `base_url` enables the usage of relative urls in `@page-url` annotations.

```php
<?php
use Behat\Mink\Driver\Selenium2Driver;
use Behat\Mink\Session;
use QATools\QATools\BEM\BEMPageFactory;
use QATools\QATools\PageObject\Config\Config;

$session = new Session(new Selenium2Driver());
$config = new Config(array(
	'base_url' => 'http://www.in-portal.com',
));

$page_factory = new BEMPageFactory($session, $config);

$home_page = new HomePage($page_factory);
$home_page->open();
$home_page->examplePageMethod();
```

## Page (class: BEMPage)

### Absolute url in the `@page-url` annotation

```php
<?php
use QATools\QATools\BEM\BEMPage;
use QATools\QATools\BEM\Element\Element;

/**
 * @page-url('http://www.in-portal.com/index.html')
 */
class HomePage extends BEMPage {

	/**
	 * Login Sidebox
	 *
	 * @var LoginSidebox
	 * @bem('block' => 'b-login', 'modifier' => array('location' => 'sidebar'))
	 */
	protected $loginSidebox;

	/**
	 * Name of the test
	 *
	 * @var string
	 */
	protected $myTest = '';

	public function examplePageMethod()
	{
		// html block login
		$error_message = $this->loginSidebox->login('user-b', 'password-b')->getLoginErrorMessage();
	}
}
```

### Relative url in the `@page-url` annotation

This will only be possible after having specified `base_url` in the config.

```php
<?php
/**
 * @page-url('/index.html')
 */
class HomePage extends BEMPage {
```


## LoginSidebox (class: Block)

```php
<?php
use QATools\QATools\BEM\Element\Block;
use QATools\QATools\BEM\Element\Element;

/**
 * @bem('block' => 'b-login')
 */
class LoginSidebox extends Block {

	/**
	 * @var Element
	 * @bem('element' => 'input-username')
	 */
	protected $username;

	/**
	 * @var Element
	 * @bem('element' => 'input-password', 'modifier' => array('color' => 'red'))
	 */
	protected $password;

	/**
	 * @var Element
	 * @bem('element' => 'btn-login')
	 */
	protected $loginButton;

	/**
	 * @var Element
	 * @bem('element' => 'error-msg')
	 */
	protected $loginErrorMessage;

	/**
	 * Tries to login a user
	 *
	 * @param string $username
	 * @param string $password
	 * @return LoginSidebox
	 */
	public function login($username, $password)
	{
		$this->username->setValue($username);
		$this->password->setValue($password);
		$this->loginButton->click();

		return $this;
	}

	/**
	 * Returns error message after login
	 *
	 * @return string
	 */
	public function getLoginErrorMessage()
	{
		return $this->loginErrorMessage->getText();
	}
}
```
