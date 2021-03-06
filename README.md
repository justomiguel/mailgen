# mailgen
[![npm version](https://badge.fury.io/js/mailgen.svg)](https://www.npmjs.com/package/mailgen)

A Node.js package that generates clean, responsive HTML e-mails for sending transactional mail.

> Programmatically create beautiful e-mails using plain old JavaScript.

## Demo

<img src="https://raw.github.com/eladnava/mailgen/master/screenshots/default/welcome.png" height="400" /> 
<img src="https://raw.github.com/eladnava/mailgen/master/screenshots/default/receipt.png" height="400" /> 

## Usage

First, install the package using npm:

```shell
npm install mailgen --save
```

Then, start using the package by importing and configuring it:

```js
var Mailgen = require('mailgen');

// Configure mailgen by setting a theme and your product info
var mailGenerator = new Mailgen({
    theme: 'default',
    product: {
        // Appears in header & footer of e-mails
        name: 'Mailgen',
        link: 'https://mailgen.js/'
        // Optional product logo
        // logo: 'https://mailgen.js/img/logo.png'
    }
});
```

Next, generate an e-mail using the following code:

```js
// Prepare email contents
var email = {
    body: {
        name: 'John Appleseed',
        intro: 'Welcome to Mailgen! We’re very excited to have you on board.',
        action: {
            instructions: 'To get started with Mailgen, please click here:',
            button: {
                color: 'green',
                text: 'Confirm your account',
                link: 'https://mailgen.js/confirm?s=d9729feb74992cc3482b350163a1a010'
            }
        },
        outro: 'Need help, or have questions? Just reply to this email, we\'d love to help.'
    }
};

// Generate an HTML email using mailgen
var emailBody = mailGenerator.generate(email);

// `emailBody` now contains the HTML body.
// It's up to you to send the e-mail. 
// Check out nodemailer to accomplish this: 
// https://nodemailer.com/
```

This code would output the following HTML template:

![Welcome](https://raw.github.com/eladnava/mailgen/master/screenshots/default/welcome.png) 

## More Examples

#### Reset Password E-mail

```js
// Prepare email contents
var email = {
    body: {
        name: 'John Appleseed',
        intro: 'You have received this email because a password reset request for your account was received.',
        action: {
            instructions: 'Click the button below to reset your password:',
            button: {
                color: 'red',
                text: 'Reset your password',
                link: 'https://mailgen.js/reset?s=b350163a1a010d9729feb74992c1a010'
            }
        },
        outro: 'If you did not request a password reset, no further action is required on your part.'
    }
};

// Generate an HTML email using mailgen
var emailBody = mailGenerator.generate(email);
```

##### Output

![Reset Password](https://raw.github.com/eladnava/mailgen/master/screenshots/default/reset.png) 

#### Receipt E-mail

> **Note:** Tables are not available in the plaintext version of generated e-mails.

```js
// Prepare email contents
var email = {
    body: {
        name: 'John Appleseed',
        intro: 'Your order has been processed successfully.',
        table: {
            data: [
                {
                    item: 'Node.js',
                    description: 'Event-driven I/O server-side JavaScript environment based on V8.',
                    price: '$10.99'
                },
                {
                    item: 'Mailgen',
                    description: 'Programmatically create beautiful e-mails using plain old JavaScript.',
                    price: '$1.99'
                }
            ],
            columns: {
                // Optionally, customize the column widths
                customWidth: {
                    item: '20%',
                    price: '15%'
                },
                // Optionally, change column text alignment
                customAlignment: {
                    price: 'right'
                }
            }
        },
        action: {
            instructions: 'You can check the status of your order and more in your dashboard:',
            button: {
                color: 'blue',
                text: 'Go to Dashboard',
                link: 'https://mailgen.js/confirm?s=d9729feb74992cc3482b350163a1a010'
            }
        },
        outro: 'We thank you for your purchase.'
    }
};

// Generate an HTML email using mailgen
var emailBody = mailGenerator.generate(email);
```

##### Output

![Subscription Renewal](https://raw.github.com/eladnava/mailgen/master/screenshots/default/receipt.png)

## Plaintext E-mails

To generate a [plaintext version of the e-mail](https://litmus.com/blog/best-practices-for-plain-text-emails-a-look-at-why-theyre-important), simply call `generatePlaintext()`:

```js
// Generate plaintext email using mailgen
var emailText = mailGenerator.generatePlaintext(email);
```

## Supported Themes

The following open-source themes are bundled with this package:

* `default` - [Postmark Transactional Email Templates](https://github.com/wildbit/postmark-templates)

We thank the contributing authors for creating these themes.

## Custom Themes

We use [ejs](http://ejs.co/) under the hood to inject the e-mail body you provide into the theme.

If you want to create your own custom theme, it's a good idea to base off of the [`themes/default/index.txt`](https://github.com/eladnava/mailgen/blob/master/themes/default/index.txt) file, or at least copy the variable injection logic from it to your custom theme file.

When you've got your theme ready, provide the path to it as follows:

```js
var path = require('path');
var Mailgen = require('mailgen');

// Configure mailgen by providing the path to your custom theme
var mailGenerator = new Mailgen({
    theme: {
        // Build an absolute path to the theme file within your project
        path: path.resolve('assets/mailgen/theme.html'),
        // Also (optionally) provide the path to a plaintext version of the theme (if you wish to use `generatePlaintext()`)
        plaintextPath: path.resolve('assets/mailgen/theme.txt')
    },
    // Configure your product as usual (see examples above)
    product: {}
});
```

## Go-To Actions

You can make use of Gmail's [Go-To Actions](https://developers.google.com/gmail/markup/reference/go-to-action) within your e-mails by suppling the `goToAction` object as follows:

```js
var email = {
    body: {
        // Optionally configure a Go-To Action button 
        goToAction: {
            text: 'Go to Dashboard',
            link: 'https://mailgen.com/confirm?s=d9729feb74992cc3482b350163a1a010',
            description: 'Check the status of your order in your dashboard'
        }
    }
};
```

> Note that you need to [get your sender address whitelisted](https://developers.google.com/gmail/markup/registering-with-google) before your Go-To Actions will show up in Gmail.

## Troubleshooting

1. After sending multiple e-mails to the same Gmail / Inbox address, they become grouped and truncated since they contain similar text, breaking the responsive e-mail layout.

> Simply sending the `X-Entity-Ref-ID` header with your e-mails will prevent grouping / truncation. 

## Contributing

* If you find a bug or wish to suggest a new feature, please create an issue first
* Make sure your code & comment conventions are in-line with the project's style
* Make your commits and PRs as tiny as possible - one feature or bugfix at a time
* Write detailed commit messages, in-line with the project's commit naming conventions

## License

Apache 2.0
