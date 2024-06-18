# SignUpApp

## Overview

**SignUpApp** is a Flutter application that provides a simple sign-up form for users. The app includes fields for first name, last name, and username, along with a visual progress indicator that updates as the form is filled. Once the form is completed, the user is directed to a welcome screen.

## Table of Contents

- [Getting Started](#getting-started)
- [Installation](#installation)
- [Usage](#usage)
- [Application Structure](#application-structure)
- [Code Explanation](#code-explanation)
- [Contributing](#contributing)
- [License](#license)

## Getting Started

To get started with the SignUpApp, you need to have Flutter installed on your development environment. If you don't have Flutter installed, follow the installation instructions in the [official Flutter documentation](https://flutter.dev/docs/get-started/install).

## Installation

1. **Download the zip file or clone the repo:**

   ```bash
   git clone https://github.com/erkripa/SignupFormWithLinearProgressIndicator
   cd SignupFormWithLinearProgressIndicator
   ```

2. **Fetch the required dependencies:**

   ```bash
   flutter pub get
   ```

3. **Run the app on your device or emulator:**

   ```bash
   flutter run
   ```

## Usage

### Running the Application

To run the app, ensure your device or emulator is connected and use the following command:

```bash
flutter run
```

### Navigating the Application

- Open the app, and you will see the sign-up form with input fields for first name, last name, and username.
- As you fill in each field, the progress indicator at the top of the form will update.
- Once all fields are filled, the "Sign up" button will be enabled.
- Clicking the "Sign up" button will navigate you to the Welcome Screen.

## Application Structure

```
lib/
├── main.dart           # Entry point of the Flutter application
```

- **main.dart**: Contains all the widgets and logic for the SignUpApp.

## Code Explanation

### Main Application (`SignUpApp`)

```dart
void main() => runApp(const SignUpApp());

class SignUpApp extends StatelessWidget {
  const SignUpApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      routes: {
        '/': (context) => const SignUpScreen(),
        '/welcome': (context) => const WelcomeScreen(),
      },
    );
  }
}
```

- **SignUpApp** is the root of the application.
- **MaterialApp** is configured with routes to navigate between `SignUpScreen` and `WelcomeScreen`.

### SignUp Screen (`SignUpScreen`)

```dart
class SignUpScreen extends StatelessWidget {
  const SignUpScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: Colors.grey[200],
      body: const Center(
        child: SizedBox(
          width: 400,
          child: Card(
            child: SignUpForm(),
          ),
        ),
      ),
    );
  }
}
```

- **SignUpScreen** provides a scaffold with a light grey background.
- The sign-up form is contained within a card that is centered on the screen.

### Welcome Screen (`WelcomeScreen`)

```dart
class WelcomeScreen extends StatelessWidget {
  const WelcomeScreen({super.key});

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Text(
          'Welcome!',
          style: Theme.of(context).textTheme.displayMedium,
        ),
      ),
    );
  }
}
```

- **WelcomeScreen** displays a simple welcome message centered on the screen after form completion.

### SignUp Form (`SignUpForm`)

```dart
class SignUpForm extends StatefulWidget {
  const SignUpForm({super.key});

  @override
  State<SignUpForm> createState() => _SignUpFormState();
}

class _SignUpFormState extends State<SignUpForm> {
  final _firstNameTextController = TextEditingController();
  final _lastNameTextController = TextEditingController();
  final _usernameTextController = TextEditingController();

  double _formProgress = 0;

  void _updateFormProgress() {
    var progress = 0.0;
    final controllers = [
      _firstNameTextController,
      _lastNameTextController,
      _usernameTextController
    ];

    for (final controller in controllers) {
      if (controller.value.text.isNotEmpty) {
        progress += 1 / controllers.length;
      }
    }

    setState(() {
      _formProgress = progress;
    });
  }

  void _showWelcomeScreen() {
    Navigator.of(context).pushNamed('/welcome');
  }

  @override
  Widget build(BuildContext context) {
    return Form(
      onChanged: _updateFormProgress,
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: [
          AnimatedProgressIndicator(value: _formProgress),
          Text('Sign up', style: Theme.of(context).textTheme.headlineMedium),
          Padding(
            padding: const EdgeInsets.all(8),
            child: TextFormField(
              controller: _firstNameTextController,
              decoration: const InputDecoration(hintText: 'First name'),
            ),
          ),
          Padding(
            padding: const EdgeInsets.all(8),
            child: TextFormField(
              controller: _lastNameTextController,
              decoration: const InputDecoration(hintText: 'Last name'),
            ),
          ),
          Padding(
            padding: const EdgeInsets.all(8),
            child: TextFormField(
              controller: _usernameTextController,
              decoration: const InputDecoration(hintText: 'Username'),
            ),
          ),
          TextButton(
            style: ButtonStyle(
              foregroundColor: MaterialStateProperty.resolveWith((states) {
                return states.contains(MaterialState.disabled)
                    ? null
                    : Colors.white;
              }),
              backgroundColor: MaterialStateProperty.resolveWith((states) {
                return states.contains(MaterialState.disabled)
                    ? null
                    : Colors.blue;
              }),
            ),
            onPressed: _formProgress == 1 ? _showWelcomeScreen : null,
            child: const Text('Sign up'),
          ),
        ],
      ),
    );
  }
}
```

- **SignUpForm** is a stateful widget that contains text fields for user input.
- The form tracks progress and updates the progress indicator as fields are filled.
- The "Sign up" button is enabled only when the form is fully completed.

### Animated Progress Indicator (`AnimatedProgressIndicator`)

```dart
class AnimatedProgressIndicator extends StatefulWidget {
  final double value;

  const AnimatedProgressIndicator({
    super.key,
    required this.value,
  });

  @override
  State<AnimatedProgressIndicator> createState() {
    return _AnimatedProgressIndicatorState();
  }
}

class _AnimatedProgressIndicatorState extends State<AnimatedProgressIndicator>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  late Animation<Color?> _colorAnimation;
  late Animation<double> _curveAnimation;

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: const Duration(milliseconds: 1200),
      vsync: this,
    );

    final colorTween = TweenSequence([
      TweenSequenceItem(
        tween: ColorTween(begin: Colors.red, end: Colors.orange),
        weight: 1,
      ),
      TweenSequenceItem(
        tween: ColorTween(begin: Colors.orange, end: Colors.yellow),
        weight: 1,
      ),
      TweenSequenceItem(
        tween: ColorTween(begin: Colors.yellow, end: Colors.green),
        weight: 1,
      ),
    ]);

    _colorAnimation = _controller.drive(colorTween);
    _curveAnimation = _controller.drive(CurveTween(curve: Curves.easeIn));
  }

  @override
  void didUpdateWidget(oldWidget) {
    super.didUpdateWidget(oldWidget);
    _controller.animateTo(widget.value);
  }

  @override
  Widget build(BuildContext context) {
    return AnimatedBuilder(
      animation: _controller,
      builder: (context, child) => LinearProgressIndicator(
        value: _curveAnimation.value,
        valueColor: _colorAnimation,
        backgroundColor: _colorAnimation.value?.withOpacity(0.4),
      ),
    );
  }
}
```

- **AnimatedProgressIndicator** animates the progress bar's value and color as the form progress updates.
- It uses an animation controller to manage the animation of the progress bar.

## Contributing

If you would like to contribute to the project, feel free to fork the repository and submit a pull request. Please ensure your contributions follow the coding style and conventions used in the project.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

This `README.md` provides a detailed guide for understanding, installing, and using the SignUpApp, as well as an explanation of its structure and components.
