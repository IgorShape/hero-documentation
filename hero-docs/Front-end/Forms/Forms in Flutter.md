# Forms in Flutter  
  
created: 10.04.25  
updated: 10.04.25  
[source](https://docs.flutter.dev/cookbook/forms/validation)  

The Form [Widget](Widget) is a container for grouping and validating multiple form fields.
A `GlobalKey` is needed to uniquely identify the form and to validate it later.
Needs to be a [Stateful Widget](Stateful%20Widget) to create a `GlobalKey<FormState>()` once and to be accessed in different points, if stored as a variable.
```dart
import 'package:flutter/material.dart';

// Define a custom Form widget.
class MyCustomForm extends StatefulWidget {
  const MyCustomForm({super.key});

  @override
  MyCustomFormState createState() {
    return MyCustomFormState();
  }
}

// Define a corresponding State class.
// This class holds data related to the form.
class MyCustomFormState extends State<MyCustomForm> {
  // Create a global key that uniquely identifies the Form widget
  // and allows validation of the form.
  //
  // Note: This is a `GlobalKey<FormState>`,
  // not a GlobalKey<MyCustomFormState>.
  final _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    // Build a Form widget using the _formKey created above.
    return Form(
      key: _formKey,
      child: const Column(
        children: <Widget>[
          // Add TextFormFields and ElevatedButton here.
        ],
      ),
    );
  }
}
```

[TextFormField](TextFormField) are used for users to enter text.
Has `validator` parameter to take a function to set rules for validation.
	If it is not valid, then it returns an error message
Example shows text to display when the form field is empty:
```dart
TextFormField(
  // The validator receives the text that the user has entered.
  validator: (value) {
    if (value == null || value.isEmpty) {
      return 'Please enter some text';
    }
    return null;
  },
),
```

Button for validation and submitting the form:
```dart
ElevatedButton(
  onPressed: () {
    // Validate returns true if the form is valid, or false otherwise.
    if (_formKey.currentState!.validate()) {
      // If the form is valid, display a snackbar. In the real world,
      // you'd often call a server or save the information in a database.
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(content: Text('Processing Data')),
      );
    }
  },
  child: const Text('Submit'),
),
```
Using the `_formKey` defined in `MyCustomFormState` you can access the `FormState` and call the `validate()` method.
	This runs all the `validator` methods in each form field.
		If everything is valid then return `true`, we can also display a `snackBar` for example
	If any fail validation, the `Form` rebuilds itself and displays any error messages and returns `false`
