SwiftValidator
===============

[![Build Status](https://travis-ci.org/jpotts18/SwiftValidator.svg?branch=travis-ci)](https://travis-ci.org/jpotts18/SwiftValidator) [![Carthage compatible](https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat)](https://github.com/Carthage/Carthage) [![codecov.io](https://codecov.io/github/jpotts18/SwiftValidator/coverage.svg?branch=master)](https://codecov.io/github/jpotts18/SwiftValidator?branch=master)

Swift Validator is a rule-based validation library for Swift.

![Swift Validator](/swift-validator-v2.gif)

## Supported UIViews

* `UITextField`
* `UITextView`
* `UISegmentedControl`
* `UIStepper`

## Core Concepts

* ``UIView`` + ``[Rule]`` + (and optional error ``UILabel``) go into  ``Validator``
* ``UIView`` + ``ValidationError`` come out of ``Validator``
* ``Validator`` evaluates ``[Rule]`` sequentially and stops evaluating when a ``Rule`` fails. 

## Installation

```ruby
# Podfile
source 'https://github.com/CocoaPods/Specs.git'
platform :ios, "8.1"

use_frameworks!
pod 'SwiftValidator', '3.0.3' 
```

Install into your project:

```bash
$ pod install
```

Open your project in Xcode from the .xcworkspace file (not the usual project file):

```bash
$ open MyProject.xcworkspace
```

If you are using Carthage you will need to add this to your `Cartfile`

```bash
github "jpotts18/SwiftValidator"
```

## Usage 

You can now import SwiftValidator framework into your files.

Initialize the ``Validator`` by setting a delegate to a View Controller or other object.

```swift
// ViewController.swift
import SwiftValidator

let validator = Validator()
```

Register the fields that you want to validate

```swift
override func viewDidLoad() {
	super.viewDidLoad()

	// Validation Rules are evaluated from left to right.
	validator.registerField(fullNameTextField, rules: [RequiredRule(), FullNameRule()])
	
	// You can pass in error labels with your rules
	// You can pass in custom error messages to regex rules (such as ZipCodeRule and EmailRule)
	validator.registerField(emailTextField, errorLabel: emailErrorLabel, rules: [RequiredRule(), EmailRule(message: "Invalid email")])
	
	// You can validate against other fields using ConfirmRule
	validator.registerField(emailConfirmTextField, errorLabel: emailConfirmErrorLabel, rules: [ConfirmationRule(confirmField: emailTextField)])
	
	// You can now pass in regex and length parameters through overloaded contructors
	validator.registerField(phoneNumberTextField, errorLabel: phoneNumberErrorLabel, rules: [RequiredRule(), MinLengthRule(length: 9)])
	validator.registerField(zipcodeTextField, errorLabel: zipcodeErrorLabel, rules: [RequiredRule(), ZipCodeRule(regex = "\\d{5}")])

	// You can unregister a text field if you no longer want to validate it
	validator.unregisterField(fullNameTextField)

	// Other UIViews
	validator.registerField(genderSegmentedControl, errorLabel: genderErrorLabel, rules: [SegmentedControlRequiredRule()])
        
	validator.registerField(notesTextView, errorLabel: notesErrorLabel, rules: [RequiredRule(), MinLengthRule(length: 10)])
        
	validator.registerField(counterStepper, errorLabel: counterStepperErrorLabel, rules: [GreaterThanRule(value: 10)])
}
```


Validate Fields on button tap or however you would like to trigger it. 

```swift
@IBAction func signupTapped(sender: AnyObject) {
	validator.validate(delegate:self)
}
```

Implement the Validation Delegate in your View controller

```swift
// ValidationDelegate methods

func validationSuccessful() {
	// submit the form
}

func validationFailed(textFieldErrors: [UITextField: ValidationError], textViewErrors: [UITextView: ValidationError], segmentedControlErrors: [UISegmentedControl: ValidationError], stepperErrors: [UIStepper: ValidationError]) {
	// turn the fields to red
	for (field, error) in validator.textFieldErrors {
		field.layer.borderColor = UIColor.redColor().CGColor
		field.layer.borderWidth = 1.0
		error.errorLabel?.text = error.errorMessage // works if you added labels
		error.errorLabel?.hidden = false
	}
}

```

## Single Field Validation

You may use single field validation in some cases. This could be useful in situations such as controlling responders:

```swift
// Don't forget to use UITextFieldDelegate
// and delegate yourTextField to self in viewDidLoad()
func textFieldShouldReturn(textField: UITextField) -> Bool {
    validator.validateField(textField){ error in
        if error == nil {
            // Field validation was successful
        } else {
            // Validation error occurred
        }
    }
    return true
}
```

## Custom Validation 

We will create a ```SSNRule``` class to show how to create your own Validation. A United States Social Security Number (or SSN) is a field that consists of XXX-XX-XXXX. 

Create a class that inherits from RegexRule

```swift

class SSNVRule: RegexRule {

    static let regex = "^\\d{3}-\\d{2}-\\d{4}$"
	
    convenience init(message : String = "Not a valid SSN"){
	self.init(regex: SSNVRule.regex, message : message)
    }
}
```

## Documentation
Checkout the docs <a href="http://jpotts18.github.io/SwiftValidator/">here</a> via [@jazzydocs](https://twitter.com/jazzydocs).


Credits
-------

Swift Validator is written and maintained by Jeff Potter [@jpotts18](http://twitter.com/jpotts18). David Patterson [@dave_tw12](http://twitter.com/dave_tw12) actively works as a collaborator.

## Contributing

1. [Fork it](https://github.com/jpotts18/SwiftValidator/fork)
2. Create your feature branch `git checkout -b my-new-feature`
3. Commit your changes `git commit -am 'Add some feature'`
4. Push to the branch `git push origin my-new-feature`
5. Create a new Pull Request
