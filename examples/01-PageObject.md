---
layout:     page
menu-title: PageObject
title:      Examples > PageObject
categories: [examples]
---

Page with login box and currency selector.

![Example Page](/examples/example_page.png)

```html
<html>
	<head></head>
	<body>
		<div class="header">
			Language:
			<select name="language">
				<option value="1">English</option>
				<option value="2">Russian</option>
			</select>
			Currency:
			<select name="curr_iso">
				<option value="USD">USD</option>
				<option value="EUR">EUR</option>
			</select>
		</div>
		<div id="block-sidebar">
			<div id="login-sidebox">
				<div class="field-error"></div>
				E-mail or Username: <input type="text" name="u.login-sidebox[-2][UserLogin]" value=""/><br/>
				Password: <input type="password" name="u.login-sidebox[-2][UserPassword]" value=""/><br/>
				<input type="submit" name="events[u.login-sidebox][OnLogin]" value="Login"/>
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
use QATools\QATools\PageObject\PageFactory;

$session = new Session(new Selenium2Driver());
$page_factory = new PageFactory($session);

$home_page = new HomePage($page_factory);
$home_page->open();
$home_page->setUsername('example user');
```

### Creating Page Factory with a Config

Providing a Config and specifying `base_url` enables the usage of relative urls in `@page-url` annotations.

```php
<?php
use Behat\Mink\Driver\Selenium2Driver;
use Behat\Mink\Session;
use QATools\QATools\PageObject\Config\Config;
use QATools\QATools\PageObject\PageFactory;

$session = new Session(new Selenium2Driver());
$config = new Config(array(
	'base_url' => 'http://www.in-portal.com',
));

$page_factory = new PageFactory($session, $config);

$home_page = new HomePage($page_factory);
$home_page->open();
$home_page->setUsername('example user');
```


## Page (class: Page)

### Absolute url in the `@page-url` annotation

```php
<?php
use QATools\QATools\PageObject\Page;
use QATools\QATools\PageObject\Element\WebElement;
use QATools\QATools\PageObject\Element\WebElementCollection;

/**
 * @page-url('http://www.in-portal.com/index.html')
 */
class HomePage extends Page {

	/**
	 *
	 * @var WebElement
	 * @find-by('name' => 'u.login-sidebox[-2][UserLogin]')
	 */
	protected $inputByName;

	/**
	 *
	 * @var WebElement
	 * @find-by('css' => 'select[name="curr_iso"]')
	 */
	protected $selectByTagName;

	/**
	 *
	 * @var WebElementCollection
	 * @find-by('css' => 'select')
	 */
	protected $selectCollection;

	public function setUsername($username)
	{
		$this->inputByName->setValue($username);
		$this->selectByTagName->setValue('EUR');

		$this->inputByName->setValue('another user');

		// Need `getObject` to iterate over current collection in proxy and not a list of collection.
		foreach ( $this->selectCollection->getObject() as $select ) {
			$select->selectOption(/* ... */);
		}
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
class HomePage extends Page {
```


## LoginSidebox (class: AbstractElementContainer)

```php
<?php
use QATools\QATools\PageObject\Element\AbstractElementContainer;
use QATools\QATools\PageObject\Element\WebElement;

class LoginSidebox extends AbstractElementContainer {

	/**
	 * @var WebElement
	 * @find-by('name' => 'u.login-sidebox[-2][UserLogin]')
	 */
	protected $username;

	/**
	 * @var WebElement
	 * @find-by('name' => 'u.login-sidebox[-2][UserPassword]')
	 */
	protected $password;

	/**
	 * @var WebElement
	 * @find-by('name' => 'events[u.login-sidebox][OnLogin]')
	 */
	protected $loginButton;

	/**
	 * @var WebElement
	 * @find-by('css' => '.field-error')
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

## Sidebar (class: AbstractElementContainer)

```php
<?php
use QATools\QATools\PageObject\Element\AbstractElementContainer;

/**
 * @find-by('id' => 'block-sidebar')
 */
class Sidebar extends AbstractElementContainer {

	/**
	 * Login Sidebox
	 *
	 * @var LoginSidebox
	 * @find-by('id' => 'login-sidebox')
	 */
	protected $loginSidebox;

	/**
	 * Tries to login a user
	 *
	 * @param string $username
	 * @param string $password
	 * @return string
	 */
	public function login($username, $password)
	{
		return $this->loginSidebox->login($username, $password)->getLoginErrorMessage();
	}
}
```
