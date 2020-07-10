# Flutter FormBuilder - flutter_form_builder

This package helps in creation of Flutter Forms by providing the syntactic sugar for creating a Form Widget and reduce the boilerplate needed to build a form, validate fields, react to changes, and collect the value of the Form.

## Simple Usage
To use this plugin, add `flutter_form_builder` as a [dependency in your pubspec.yaml file](https://flutter.io/platform-plugins/).

### Example
```dart
final GlobalKey<FormBuilderState> _fbKey = GlobalKey<FormBuilderState>();
```
**Note:** Avoid defining the GlobalKey inside your build method because this will create a new GlobalKey on every build cycle bringing about some erratic behavior.

```dart
Column(
  children: <Widget>[
    FormBuilder(
      key: _fbKey,
      autovalidate: true,
      // readonly: true,
      child: Column(
        children: <Widget>[
          FormBuilderFilterChip(
            attribute: 'filter_chip',
            decoration: InputDecoration(
              labelText: 'Select many options',
            ),
            options: [
              FormBuilderFieldOption(
                  value: 'Test', child: Text('Test')),
              FormBuilderFieldOption(
                  value: 'Test 1', child: Text('Test 1')),
              FormBuilderFieldOption(
                  value: 'Test 2', child: Text('Test 2')),
              FormBuilderFieldOption(
                  value: 'Test 3', child: Text('Test 3')),
              FormBuilderFieldOption(
                  value: 'Test 4', child: Text('Test 4')),
            ],
          ),
          FormBuilderChoiceChip(
            attribute: 'choice_chip',
            decoration: InputDecoration(
              labelText: 'Select an option',
            ),
            options: [
              FormBuilderFieldOption(
                  value: 'Test', child: Text('Test')),
              FormBuilderFieldOption(
                  value: 'Test 1', child: Text('Test 1')),
              FormBuilderFieldOption(
                  value: 'Test 2', child: Text('Test 2')),
              FormBuilderFieldOption(
                  value: 'Test 3', child: Text('Test 3')),
              FormBuilderFieldOption(
                  value: 'Test 4', child: Text('Test 4')),
            ],
          ),
          FormBuilderColorPickerField(
            attribute: 'color_picker',
            // initialValue: Colors.yellow,
            colorPickerType: ColorPickerType.MaterialPicker,
            decoration: InputDecoration(labelText: 'Pick Color'),
          ),
          FormBuilderChipsInput(
            decoration: InputDecoration(labelText: 'Chips'),
            attribute: 'chips_test',
            onChanged: _onChanged,
            initialValue: [
              Contact('Andrew', 'stock@man.com',
                  'https://d2gg9evh47fn9z.cloudfront.net/800px_COLOURBOX4057996.jpg'),
            ],
            maxChips: 5,
            findSuggestions: (String query) {
              if (query.isNotEmpty) {
                var lowercaseQuery = query.toLowerCase();
                return contacts.where((profile) {
                  return profile.name
                          .toLowerCase()
                          .contains(query.toLowerCase()) ||
                      profile.email
                          .toLowerCase()
                          .contains(query.toLowerCase());
                }).toList(growable: false)
                  ..sort((a, b) => a.name
                      .toLowerCase()
                      .indexOf(lowercaseQuery)
                      .compareTo(b.name
                          .toLowerCase()
                          .indexOf(lowercaseQuery)));
              } else {
                return const <Contact>[];
              }
            },
            chipBuilder: (context, state, profile) {
              return InputChip(
                key: ObjectKey(profile),
                label: Text(profile.name),
                avatar: CircleAvatar(
                  backgroundImage: NetworkImage(profile.imageUrl),
                ),
                onDeleted: () => state.deleteChip(profile),
                materialTapTargetSize: MaterialTapTargetSize.shrinkWrap,
              );
            },
            suggestionBuilder: (context, state, profile) {
              return ListTile(
                key: ObjectKey(profile),
                leading: CircleAvatar(
                  backgroundImage: NetworkImage(profile.imageUrl),
                ),
                title: Text(profile.name),
                subtitle: Text(profile.email),
                onTap: () => state.selectSuggestion(profile),
              );
            },
          ),
          FormBuilderDateTimePicker(
            attribute: 'date',
            // onChanged: _onChanged,
            inputType: InputType.time,
            decoration: InputDecoration(
              labelText: 'Appointment Time',
            ),
            initialTime: TimeOfDay(hour: 8, minute: 0),
            // initialValue: DateTime.now(),
            // readonly: true,
          ),
          FormBuilderDateRangePicker(
            attribute: 'date_range',
            firstDate: DateTime(1970),
            lastDate: DateTime(2030),
            format: DateFormat('yyyy-MM-dd'),
            onChanged: _onChanged,
            decoration: InputDecoration(
              labelText: 'Date Range',
              helperText: 'Helper text',
              hintText: 'Hint text',
            ),
          ),
          FormBuilderSlider(
            attribute: 'slider',
            validator: FormBuilderValidators.compose([
              FormBuilderValidators.min(context, 6),
            ]),
            onChanged: _onChanged,
            min: 0.0,
            max: 10.0,
            initialValue: 7.0,
            divisions: 20,
            activeColor: Colors.red,
            inactiveColor: Colors.pink[100],
            decoration: InputDecoration(
              labelText: 'Number of things',
            ),
          ),
          FormBuilderCheckbox(
            attribute: 'accept_terms',
            initialValue: false,
            onChanged: _onChanged,
            title: RichText(
              text: TextSpan(
                children: [
                  TextSpan(
                    text: 'I have read and agree to the ',
                    style: TextStyle(color: Colors.black),
                  ),
                  TextSpan(
                    text: 'Terms and Conditions',
                    style: TextStyle(color: Colors.blue),
                    recognizer: TapGestureRecognizer()
                      ..onTap = () {
                        print('launch url');
                      },
                  ),
                ],
              ),
            ),
            validator: FormBuilderValidators.compose([
              FormBuilderValidators.requireTrue(
                context,
                errorText:
                    'You must accept terms and conditions to continue',
              ),
            ]),
          ),
          FormBuilderTextField(
            attribute: 'age',
            decoration: InputDecoration(
              labelText:
                  'This value is passed along to the [Text.maxLines] attribute of the [Text] widget used to display the hint text.',
            ),
            onChanged: _onChanged,
            // valueTransformer: (text) => num.tryParse(text),
            validator: FormBuilderValidators.compose([
              FormBuilderValidators.required(context),
              FormBuilderValidators.numeric(context),
              FormBuilderValidators.max(context, 70),
            ]),
            keyboardType: TextInputType.number,
          ),
          FormBuilderDropdown(
            attribute: 'gender',
            decoration: InputDecoration(
              labelText: 'Gender',
            ),
            // initialValue: 'Male',
            allowClear: true,
            hint: Text('Select Gender'),
            validator: FormBuilderValidators.compose(
                [FormBuilderValidators.required(context)]),
            items: genderOptions
                .map((gender) => DropdownMenuItem(
                      value: gender,
                      child: Text('$gender'),
                    ))
                .toList(),
          ),
          FormBuilderTypeAhead(
            decoration: InputDecoration(
              labelText: 'Country',
            ),
            attribute: 'country',
            onChanged: _onChanged,
            itemBuilder: (context, country) {
              return ListTile(
                title: Text(country),
              );
            },
            controller: TextEditingController(text: ''),
            initialValue: 'Uganda',
            suggestionsCallback: (query) {
              if (query.isNotEmpty) {
                var lowercaseQuery = query.toLowerCase();
                return allCountries.where((country) {
                  return country.toLowerCase().contains(lowercaseQuery);
                }).toList(growable: false)
                  ..sort((a, b) => a
                      .toLowerCase()
                      .indexOf(lowercaseQuery)
                      .compareTo(
                          b.toLowerCase().indexOf(lowercaseQuery)));
              } else {
                return allCountries;
              }
            },
          ),
          FormBuilderRadioList(
            decoration:
                InputDecoration(labelText: 'My chosen language'),
            attribute: 'best_language',
            onChanged: _onChanged,
            validator: FormBuilderValidators.compose(
                [FormBuilderValidators.required(context)]),
            options: ['Dart', 'Kotlin', 'Java', 'Swift', 'Objective-C']
                .map((lang) => FormBuilderFieldOption(
                      value: lang,
                      child: Text('$lang'),
                    ))
                .toList(growable: false),
          ),
          FormBuilderTouchSpin(
            decoration: InputDecoration(labelText: 'Stepper'),
            attribute: 'stepper',
            initialValue: 10,
            step: 1,
            iconSize: 48.0,
            addIcon: Icon(Icons.arrow_right),
            subtractIcon: Icon(Icons.arrow_left),
          ),
          FormBuilderRating(
            decoration: InputDecoration(labelText: 'Rate this form'),
            attribute: 'rate',
            iconSize: 32.0,
            initialValue: 1.0,
            max: 5.0,
            onChanged: _onChanged,
          ),
        ],
      ),
    ),
    Row(
      children: <Widget>[
        Expanded(
          child: MaterialButton(
            color: Theme.of(context).accentColor,
            child: Text(
              "Submit",
              style: TextStyle(color: Colors.white),
            ),
            onPressed: () {
              _fbKey.currentState.save();
              if (_fbKey.currentState.validate()) {
                print(_fbKey.currentState.value);
              } else {
                print("validation failed");
              }
            },
          ),
        ),
        SizedBox(
          width: 20,
        ),
        Expanded(
          child: MaterialButton(
            color: Theme.of(context).accentColor,
            child: Text(
              "Reset",
              style: TextStyle(color: Colors.white),
            ),
            onPressed: () {
              _fbKey.currentState.reset();
            },
          ),
        ),
      ],
    )
  ],
)
```

## l10n
Just add the `FormBuilderLocalizations.delegate` in the list of your app delegates

```dart
  return MaterialApp(
      supportedLocales: [
        Locale('en'),
        Locale('it'),
        Locale('fr'),
        Locale('es'),
      ],
      localizationsDelegates: [
        FormBuilderLocalizations.delegate,
        GlobalMaterialLocalizations.delegate,
        GlobalWidgetsLocalizations.delegate,
      ],
```
 


## Input widgets
The currently supported fields include:
* `FormBuilderCheckbox` - Single Checkbox field
* `FormBuilderCheckboxList` - List of Checkboxes for multiple selection
* `FormBuilderChipsInput` - Takes a list of `Chip`s as input and suggests more options on typing
* `FormBuilderChoiceChip` - Creates a chip that acts like a radio button.
* `FormBuilderColorPicker` - For `Color` input selection
* `FormBuilderCountryPicker` - Country selection from list
* `FormBuilderDateRangePicker` - For selection of a range of dates
* `FormBuilderDateTimePicker` - For `Date`, `Time` and `DateTime` input
* `FormBuilderDropdown` - Used to select one value from a list as a Dropdown
* `FormBuilderFilterChip` - Creates a chip that acts like a checkbox.
* `FormBuilderImagePicker` - Picker a image from Gallery or Camera and stores it in a List of images, File or String. **Note**: This picker is available for iOS and Android.
* `FormBuilderPhoneField` - International phone number input. 
* `FormBuilderRadio` - Used to select one value from a list of Radio Widgets 
* `FormBuilderRadioGroup` - Used to select one value from a list of Radio Widgets 
* `FormBuilderRangeSlider` - Used to select a range from a range of values
* `FormBuilderRating` - For selection of a numerical value as a rating 
* `FormBuilderSegmentedControl` - For selection of a value from the `CupertinoSegmentedControl` as an input
* `FormBuilderSignaturePad` - Presents a drawing pad on which user can doodle
* `FormBuilderSlider` - For selection of a numerical value on a slider
* `FormBuilderSwitch` - On/Off switch
* `FormBuilderTextField` - For text input. Accepts input of single-line text, multi-line text, password, email, urls etc by using different configurations and validators
* `FormBuilderTouchSpin` - Selection of a number by tapping on a plus or minus icon
* `FormBuilderTypeAhead` - Auto-completes user input from a list of items

In order to create an input field in the form, along with the label, and any applicable validation, there are several attributes that are supported by all types of inputs namely:

| Attribute | Type  | Default | Required | Description |
|-----------|-------|---------|-------------|----------|
| `attribute` | `String` | `null` | `true` | This will form the key in the form value Map |
| `initialValue` | `dynamic` | `null`  | `false` | The initial value of the input field |
| `readOnly` | `bool` | `false` | `false` | Determines whether the field widget will accept user input. This value will be ignored if the `readOnly` attribute of `FormBuilder` widget is set to `true` |
| `decoration` | `InputDecoration` | `InputDecoration()` | `false` | |
| `validator` | `FormFieldValidator` | `null` | `false` | A `FormFieldValidator` that will check the validity of value in the `FormField` |
| `onChanged` | `ValueChanged<T>` | `null` | `false` | This event function will fire immediately the the field value changes |
| `valueTransformer` | `ValueTransformer<T>` | `null` | `false` | Function that transforms field value before saving to form value. e.g. transform TextField value for numeric field from `String` to `num` |
The rest of the attributes will be determined by the type of Widget being used.

### Building your own custom field
To build your own field within a `FormBuilder`, we use `FormBuilderField` which will require that you define your own field.
```dart
var options = ["Option 1", "Option 2", "Option 3"];
```

```dart
FormBuilderField(
  attribute: "name",
  validator: FormBuilderValidators.compose([
    FormBuilderValidators.required(context),
  ]),
  builder: (FormFieldState<dynamic> field) {
    return InputDecorator(
      decoration: InputDecoration(
        labelText: "Select option",
        contentPadding:
            EdgeInsets.only(top: 10.0, bottom: 0.0),
        border: InputBorder.none,
        errorText: field.errorText,
      ),
      child: Container(
        height: 200,
        child: CupertinoPicker(
          itemExtent: 30,
          children: options.map((c) => Text(c)).toList(),
          onSelectedItemChanged: (index) {
            field.didChange(options[index]);
          },
        ),
      ),
    );
  },
),
```

## Validation
The `validator` attribute in fields take in a `FormFieldValidator` which checks the validity of the field. A `FormFieldValidator` returns `null` if validation is successful and a `String` for the `errorText` if validation fails.

### Built-in Validators
This package comes with several most common `FormFieldValidator`s such as required, numeric, mail, URL, min, 
max, minLength, maxLength, IP, credit card etc. with default `errorText` in English but with 
ability to include you own error message that will display whenever validation fails.

Available built-in validators include:
* `FormBuilderValidators.required()` - requires the field have a non-empty value.
* `FormBuilderValidators.numeric()` - requires the field's value to be a valid number.
* `FormBuilderValidators.min()` - requires the field's value to be greater than or equal to the provided number.
* `FormBuilderValidators.max()` - requires the field's value to be less than or equal to the provided number.
* `FormBuilderValidators.minLength()` - requires the length of the field's value to be greater than or equal to the provided minimum length.
* `FormBuilderValidators.maxLength()` - requires the length of the field's value to be less than or equal to the provided maximum length.
* `FormBuilderValidators.pattern()` - requires the field's value to match the provided regex pattern.
* `FormBuilderValidators.email()` - requires the field's value to be a valid email address.
* ``FormBuilderValidators.url()`` - requires the field's value to be a valid url.
* `FormBuilderValidators.IP()` - requires the field's value to be a valid IP address.
* `FormBuilderValidators.creditCard()` - requires the field's value to be a valid credit card number.
* `FormBuilderValidators.date()` - requires the field's value to be a valid date string.
* `FormBuilderValidators.requiredTrue()` - requires the field's value be true.

### Using multiple validators
`FormBuilderValidators` class comes with a very useful static function named `compose()` which takes in any number of `FormFieldValidator` functions. On validation each validator is run and if any returns a non-null value (i.e. a String), validation fails and the `errorText` for the field is set as the returned string.

Validation example:
```dart
FormBuilderTextField(
  attribute: "age",
  decoration: InputDecoration(labelText: "Age"),
  validator: FormBuilderValidators.compose([
    FormBuilderValidators.numeric(context, errorText: "La edad debe ser numérica."),
    FormBuilderValidators.max(context, 70),
    (val){
      if(val < 0)
        return 'We cannot have a negative age';
      return null;
    }
  ]),
),
```

### Programmatically inducing an error
Declare a variable to hold your error:
```dart
String _emailError;
```

Use the variable as the `errorText` within `InputDecoration`
```dart
FormBuilderTextField(
  attribute: 'email',
  decoration: InputDecoration(
    labelText: 'Email', 
    errorText: _emailError,
  ),
  validator: FormBuilderValidators.compose([
      FormBuilderValidators.required(context),
      FormBuilderValidators.email(context),
  ]),
),
```

Set the error text
```dart
RaisedButton(
  child: Text('Submit'),
  onPressed: () async {
    if(checkIfEmailExists()){
      setState(() => _emailError = 'Email already taken.');
    }
  },
),
```


### Conditional validation
You can also validate a field based on the value of another field
```
FormBuilderRadio(
  decoration: InputDecoration(labelText: 'My best language'),
  attribute: "best_language",
  validator: FormBuilderValidators.required(context),
  options: [
    "Dart",
    "Kotlin",
    "Java",
    "Swift",
    "Objective-C",
    "Other"
  ]
      .map((lang) => FormBuilderFieldOption(value: lang))
      .toList(growable: false),
),
FormBuilderTextField(
    attribute: "specify",
    decoration: InputDecoration(labelText: "If Other, please specify"),
    validator:
      (val){
          if(_fbKey.currentState.fields['best_language'].currentState.value == "Other" && (val == null || val.isEmpty))
              return "Kindly specify your language";
      },
),
```

## CREDITS
### Contributors
<a href="https://github.com/danvick/flutter_form_builder/graphs/contributors">
  <img src="https://contributors-img.firebaseapp.com/image?repo=danvick/flutter_form_builder" />
</a>

Made with [contributors-img](https://contributors-img.firebaseapp.com).

### Dependencies
This package is dependent on the following packages and plugins:
* [country_code_picker](https://github.com/anicdh/country_code_picker) by [](https://github.com/)
* [date_range_picker](https://github.com/anicdh/date_range_picker) by [anicdh](https://github.com/anicdh)
* [datetime_picker_formfield](https://pub.dev/packages/datetime_picker_formfield) by [Jacob Phillips](https://github.com/jifalops)
* [flutter_colorpicker](https://pub.dev/packages/flutter_colorpicker) by [mchome](https://github.com/mchome)
* [flutter_typeahead](https://pub.dev/packages/flutter_typeahead) by [AbdulRahmanAlHamali](https://github.com/AbdulRahmanAlHamali)
* [phone_number](https://pub.dev/packages/phone_number) by [](https://github.com/)
* [rating_bar](https://pub.dev/packages/rating_bar) by [Joshua Matta](https://github.com/joshmatta)
* [signature](https://pub.dev/packages/signature) by [4Q s.r.o.](https://github.com/4Q-s-r-o)
* [validators](https://pub.dev/packages/validators) by [dart-league](https://github.com/dart-league)
* [flutter_chips_input](https://pub.dev/packages/flutter_chips_input) & [flutter_touch_spin](https://pub.dev/packages/flutter_touch_spin) by [Yours truly :-)](https://github.com/danvick)

## SUPPORT
### PRs
Any kind of support in the form of Pull Requests are always appreciated.

We especially welcome efforts to internationalize/localize the package by translating the default validation error texts.

### Coffee :-)
If this package was helpful to you in delivering your project or you just wanna to support this project, a cup of coffee would be highly appreciated ;-)

[![Buy me a coffee](https://www.buymeacoffee.com/assets/img/custom_images/purple_img.png)](https://buymeacoff.ee/wb5M9y2Sz)
