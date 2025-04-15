# Forms in Hero  
created: 10.04.25  
updated: 10.04.25  

A `TextEditingController` is used for user input for text form fields.
There are also other [Controllers](Controllers) such as `PhoneController`.

Hero has custom form fields to ensure consistency of design across forms.
See:
* [CustomTextFormField](CustomTextFormField)
* [CustomPhoneNumberField](CustomPhoneNumberField)
* [OptionFormField](OptionFormField)
* [ImageFormField](ImageFormField)
* [CustomDropDownFormField](CustomDropDownFormField)
* [DateRangeFormField](DateRangeFormField)
* [DateFormField](DateFormField)
* [CustomCheckboxFormField](CustomCheckboxFormField)
* [TrueFalseFormField](TrueFalseFormField)
* [AddressFormField](AddressFormField)

State management in forms:
* [Stateful Widget](Stateful%20Widget) for handling validation
	* See [Forms in Flutter](Forms%20in%20Flutter.md)
* [BLoC](BLoC) for handling output and state of page

#### BLoC in Forms
Typical events in Form Blocs are when buttons are pressed for output.
Example sign in:
```dart
class AttemptSignin extends SigninFormEvent {
	final SignupFormData data;
	AttemptSignin({required this.data});
}
```
Typical bloc logic calls the API to send data from form to backend to be processed.
Example sign in:
```dart
on<AttemptSignin>((event, emit) async {
	emit(SigninFormProcessing());
	try {
		HeroAppUser user = await authRepo.signInEmailAndPassword(
		event.data.email!,	
		event.data.password!,
	);
		emit(SigninFormSuccess(user: user));
	} catch (e) {
		emit(SigninFormError(message: e.toString()));
	}
});
```
Typical states in Form Blocs 
```dart
final class FormInitial extends FormState {}

final class FormProcessing extends FormState {}

final class FormSuccess extends FormState {
	// send whatever front end needs from success
}

final class FormError extends FormState {
	final String message;
	FormError({required this.message});
}
```

#### BLoC In View 
`BlocListener` are used to trigger methods from state.
In this case we use it to listen to a successful sign in form submission, set the user and set the page to create company for the user.
```dart
listener: (context, state) {
	if (state is SigninFormSuccess) {
		context
			.read<AuthenticationBloc>()
			.add(SetCurrentUser(user: state.user));
		context.goNamed(
			widget.routeToNavigateToAfterSignin ?? "root",
		);
	}
},
```
Use a `BlocBuilder` to hide the form and display a loading indicator to when the form is processing. This ensures the user can't edit the fields once they've submitted. Otherwise display the `Form`.
```dart
if (state is SigninFormProcessing) {
	return Center(
		child: CircularProgressIndicator(),
	);
}
return Form(
	...
```
Can also use a `BlocBuilder` to display error messages from bloc logic.
Example error message being displayed above submit button and below the last form field:
```dart
CustomTextFormField(
  label: 'Password',
  controller: passwordController,
  helperText: 'Required',
  obscureText: true,
  validator: (newString) {
    if (newString == null || newString.isEmpty) {
      return 'This is a required field';
    }
    return null;
  },
),

Gap(AppSpacing.medium),

BlocBuilder<SigninFormBloc, SigninFormState>(
  builder: (context, state) {
    if (state is SigninFormError) {
      return Column(
        children: [
          ErrorMessageField(errorMessage: state.message).makeFullWidth(),
          Gap(AppSpacing.medium),
        ],
      );
    } else {
      return Container();
    }
  },
),

```
