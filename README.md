reCAPTCHA
===============

[![Build Status](https://img.shields.io/travis/minetro/reCAPTCHA.svg?style=flat-square)](https://travis-ci.org/minetro/reCAPTCHA)
[![Code coverage](https://img.shields.io/coveralls/minetro/reCAPTCHA.svg?style=flat-square)](https://coveralls.io/r/minetro/reCAPTCHA)
[![Downloads this Month](https://img.shields.io/packagist/dm/minetro/recaptcha.svg?style=flat-square)](https://packagist.org/packages/minetro/recaptcha)
[![Downloads total](https://img.shields.io/packagist/dt/minetro/recaptcha.svg?style=flat-square)](https://packagist.org/packages/minetro/recaptcha)
[![Latest stable](https://img.shields.io/packagist/v/minetro/recaptcha.svg?style=flat-square)](https://packagist.org/packages/minetro/recaptcha)
[![HHVM Status](https://img.shields.io/hhvm/minetro/reCAPTCHA.svg?style=flat-square)](http://hhvm.h4cc.de/package/minetro/reCAPTCHA)

Google reCAPTCHA implementation for Nette Framework.

# Pre-install

Add your site to the sitelist in reCAPTCHA administration.

https://www.google.com/recaptcha/admin#list

# Install

## Nette >=2.3
```sh
$ composer require minetro/recaptcha:~1.5.0
```

## Nette 2.2 
```sh
$ composer require minetro/recaptcha:~1.4.0
```

![reCAPTCHA](https://raw.githubusercontent.com/minetro/recaptcha/master/recaptcha.png)

# Configuration

## Automatic
```yaml
extensions:
    recaptcha: Minetro\Forms\reCAPTCHA\ReCaptchaExtension
    
recaptcha:
    secretKey: ***
    siteKey: ***
```
## Manual

### NEON - parameters
```yaml
# reCAPTCHA
parameters:
    reCAPTCHA:
        siteKey: ***key**
        secretKey: ***key**
```

### NEON - services
```yaml
services:
    reCAPTCHA.validator:
        class: Minetro\Forms\reCAPTCHA\ReCaptchaValidator
        implement: Minetro\Forms\reCAPTCHA\IReCaptchaValidatorFactory
        arguments: [%reCAPTCHA.secretKey%]
    
    reCAPTCHA.holder:
        factory: Minetro\Forms\reCAPTCHA\ReCaptchaHolder::factory(%reCAPTCHA.siteKey%)
        tags: [run]
```

You should call `ReCaptchaBinding::bind(%reCAPTCHA.siteKey%)`, if you want use native `$form->addReCaptcha()` method.

# Usage 

## Forms
```php
use Minetro\Forms\reCAPTCHA\ReCaptchaField;
use Minetro\Forms\reCAPTCHA\ReCaptchaHolder;
use Nette\Application\UI\Form;

class MyForm extends Form
{

    /**
     * @param  string  $name   Field name
     * @param  string  $label  Html label
     * @return ReCaptchaField
     */
    public function addReCaptcha($name = 'recaptcha', $label = NULL)
    {
        return $this[$name] = new ReCaptchaField(ReCaptchaHolder::getSiteKey(), $label);
    }

}
```

## Presenter/Control
```php
use Minetro\Forms\reCAPTCHA\ReCaptchaField;
use Minetro\Forms\reCAPTCHA\IReCaptchaValidatorFactory;
use Nette\Application\UI\Form;

/** @var IReCaptchaValidatorFactory @inject */
public $reCaptchaValidatorFactory;

/**
 * @return Form
 */
protected function createComponentForm() 
{
    $form = new Form();
    
    $form['recaptcha'] = $recaptcha = new ReCaptchaField($this->siteKey, $label = NULL); 
    
    $validator = $this->reCaptchaValidatorFactory->create();
    $recaptcha->addRule([$validator, 'validateControl'], 'Vypadá to, že nejste člověk.');
    
    // ...
}
```

## JavaScript

Before `</body>` element.

```html
<!-- re-Captcha -->
<script src='https://www.google.com/recaptcha/api.js'></script>
```

