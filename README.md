# React Native Guidelines

Clean code and a clear project structure make everything better. We want our code to be readable, clean, and to the point. A fellow programmer should be able to read your code and understand it without having to refactor it first or spend hours trying to understand what you were trying to achieve. You'll also make it easier on yourself, in case you need to revisit old code, if you wrote it nicely to begin with.

Here are my recommended React Native Guidelines that will help you and your team to write great code.

## 1. Project Structure

- Place tests in `__tests__`
- Place assets in `assets/`
  - Place images their own folder `assets/images/`
- Place JavaScript code in `src/`
  - Place code that extends or manages dependencies in `src/lib/`
  - Place components in `src/components/`. There should be only one component per file.
    - For components that import component specific files, create a folder for the component - `src/component/results/` and place component code in `src/component/results/index.js`. Add component specific files to the same folder `src/component/results/InvestmentResults.js`.
    - For standalone components, place them in the components folder `src/components/Home.js`
  - Place components that are used in multiple places in `src/components/common/`

## 1.2 File naming

### 1.2.1 Folders

Folders are written in Dash Case (e.g., `dash-case`).

### 1.2.2 Components

Component names are written in [UpperCamelCase](http://en.wikipedia.org/wiki/CamelCase), except when the file is named index.js.

### 1.2.3 Helper files and assets

Helper files (e.g., `utils.js` and `constants.js`) are written in **plural**.

Assets (e.g., `questionMark.png`) along with helper files are written in [lowerCamelCase](http://en.wikipedia.org/wiki/CamelCase).

---

# 2. Code Guidelines

## 2.1 Formatting

The code should be consistently formatted. Consistent code reduces syntax related distractions and helps us focus on the important logic. Use ESLint to format your code and configure the rules by extending [Airbnb's style guide](https://github.com/airbnb/javascript/blob/master/react/README.md). To make formatting easier, you can use [Prettier](https://github.com/prettier/prettier-vscode) and integrate it with ESLint.

To maintain great code quality and prevent bad commits, use [husky](https://www.npmjs.com/package/husky) to ensure all code is linted and unit tests pass before pushing.

## 2.2 Tests

Every component should have a corresponding snapshot test created with [Jest](https://facebook.github.io/jest/docs/en/snapshot-testing.html). This helps us to maintain desired UI and prevent unwanted changes (e.g., when altering a widely used component). Tests should be placed in the `__tests__` folder, following the project's file structure and matching the name of the component the test is targeting.

**Example:**

`__test__/components/Home.test.js`
`__test__/components/results/Results.test.js`

Write unit tests for your util functions and functions inside components. I recommend using [enzyme](https://github.com/airbnb/enzyme) and [sinon](https://sinonjs.org/) for this purpose.

[SonarQuebe](https://www.sonarqube.org/) is a great tool to help you maintain your test coverage and detect any issues.

## 2.3 Helper files

### 2.3.1 Constants

Keep all constants that are used more than once in a constants.js file.

### 2.3.2 Strings

It's a good practice to keep all strings in the same place. If you ever need to localize your app, a stringConstants file comes in handy. Also if you have common strings that are used multiple times, you can group them together. This decreases the likelihood of typos and makes it easier to replace strings if you need to in the future. Place your strings in `stringConstants.js`, order alphabetically, and group strings together based on where they're displayed. Some strings might be used in multiple places in the app. To make it easier to reuse strings, store them in Uppper Camel Case and utilized functions such as `toUpperCase` or `toLowerCase` when another string form is needed.

**Example:**

```javascript
// Note the value of the field is the same as the name to offer clarity and avoid duplication issues
export const commonStrings = {
  accept: 'Accept',
  cancel: 'Cancel'
};

export const someScreenStrings = {
  title: 'Title'
};
```

### 2.3.3 Styles

To prevent typos, keep all your styles in one place, `styles.js`, ordered alphabetically.

**Example:**

```javascript
export const color = {
  gray: '#8b8b8b',
  green: '#006a35'
};

export const fontFamily = {
  arial: 'Arial',
  timesNewRoman: 'TimesNewRoman'
};
```

### 2.3.4 Common functions

Place functions that are used multiple times in the app in `utils.js`. This makes it easy to test the functions and enables reusability.

## 2.4 Styling components

You can use React Native's [StyleSheet](https://facebook.github.io/react-native/docs/stylesheet) to style your components. Another alternative are [Styled Components](https://www.styled-components.com/). Styled Components allow you to write actual CSS code to style your components. It also introduces clarity as it removes the mapping between components and styles. Styled Components also allow you to extend existing styles which offers reusability.

**Example:**

```javascript
const BlueText = styled.Text`
  color: blue;
`;

const LargeBlueText = BlueText.extend`
  font-size: 16;
`;

const WhiteView = styled.View`
  background-color: white;
`;

class MyComponent extends React.Component {
  render() {
    return (
      <WhiteView>
        <LargeBlueText>My Component</LargeBlueText>
        <BlueText>Hello World!</BlueText>
      </WhiteView>
    );
  }
}
```

## 2.5 Programming rules

# 2.5.1 Destructuring

Destructuring props simplifies the code and allows us to see clearly what's going on in the code.

**Example:**

```javascript
// Bad
class MyComponent extends React.Component {
  render() {
    return (
      <View>
        <Text>{this.props.title}</Text>
        <Text>{this.props.message}</Text>
      </View>
    );
  }
}

// Good
class MyComponent extends React.Component {
  render() {
    const { title, message } = this.props;
    return (
      <View>
        <Text>{title}</Text>
        <Text>{message}</Text>
      </View>
    );
  }
}
```

# 2.5.2 Conditional Rendering

Use short-circuit evaluation for conditional rendering for one condition

**Example:**

```javascript
// Bad
class MyComponent extends React.Component {
  render() {
    return isTrue ? <Text>True</Text> : null;
  }
}

// Good
class MyComponent extends React.Component {
  render() {
    return isTrue && <Text>True<Text>;
  }
}
```

When you have more than one condition. Extract some of the logic to other components. This allows us to reuse the components. You can also split the logic into functions. That offers a documentational value and makes the code more readable. Please avoid nested ternaries like hot fire. They do not contribute to code readability.

**Example - Extract logic to a separate component:**

```javascript
// Bad
const BlueButton = styled.TouchableOpacity`
  background-color: blue;
`;

const RedButton = styled.TouchableOpacity`
  background-color: red;
`;

class MyComponent extends React.Component {
  render() {
    const { isRed } = this.props;
    return isRed ? <RedButton>Red</RedButton> : <BlueButton>Blue</BlueButton>;
  }
}

// Good
// CustomButton.js
const StyledButton = styled.TouchableOpacity`
  background-color: ${props => props.backgroundColor};
`;

function CustomButton({ backgroundColor, title }) {
  return (
    <StyledButton backgroundColor={backgroundColor}>
      <Text>{title}</Text>
    </StyledButton>
  );
}

CustomButton.propTypes = {
  backgroundColor: PropTypes.string.isRequired,
  title: PropTypes.string.isRequired
};

export default CustomButton;

// MyComponent.js
import CustomButton from './CustomButton.js';
import { capitalizeFirstLetter } from '../utils.js'; // Helper function

class MyComponent extends React.Component {
  render() {
    const { isRed } = this.props;
    const color = isRed ? 'red' : 'blue';
    return <CustomButton backgroundColor={color} title={capitalizeFirstLetter(color)} />;
  }
}
```

**Example - Extract logic to a function:**

```javascript
// Bad
class MyComponent extends React.Component {
  render() {
    const { isSunny, isRaining } = this.props;
    return isSunny ? (
      isRaining ? (
        <Text>Take the umbrella and go outside!</Text>
      ) : (
        <Text>Go outside, it's sunny!</Text>
      )
    ) : (
      <Text>Stay in!</Text>
    );
  }
}

// Good
class MyComponent extends React.Component {
  renderRainingCondition = () => {
    const { isRaining } = this.props;
    return isRaining ? <Text>Take the umbrella and go outside!</Text> : <Text>Go outside, it's sunny!</Text>;
  };

  render() {
    const { isSunny, isRaining } = this.props;
    return isSunny ? this.renderRainingCondition() : <Text>Stay in!</Text>;
  }
}
```

# 2.5.3 Closures

Create a new function instead of passing closures to components. If we pass closures directly to the component, every time the parent component renders, a new function is created and passed to the subcomponent (TouchableOpacity in the example). This also automatically triggers the subcomponent to re-render, regardless of whether its other props have actually changed. Also, by passing in a function, we make the code easier to read.

**Example:**

```javascript
// Bad
class MyComponent extends React.Component {
  render() {
    const { navigation } = this.props;
    return (
      <TouchableOpacity
        onPress={() => {
          const title = 'New Title';
          navigation.navigate({ routeName: 'SomeScreen', params: { title } });
        }}
      />
    );
  }
}

// Good
class MyComponent extends React.Component {
  navigateToSomeScreen = () => {
    const title = 'New Title';
    navigation.navigate({ routeName: 'SomeScreen', params: { title } });
  };

  render() {
    const { navigation } = this.props;
    return <TouchableOpacity onPress={this.navigateToSomeScreen} />;
  }
}
```

## 2.6 Third-Party Libraries

The React Native community is fast paced and therefore we have to be careful when using third-party libraries. Make sure that the library is well received within the community and that it's being maintained. You can evaluate this by the number of open issues and how many stars the repo has. We categorize libraries as unmaintained when the last commit was more than 6 months ago.

After you've decided to use a library, make sure to run `yarn outdated` every once in a while or use a tool such as [Greenkeeper](https://greenkeeper.io/) to make sure that you're up to date with the latest versions of your packages.

## 3. Development Process

## 3.1 Workflow

Use [Gitflow workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow) as a branching model. This structure provides a robust framework for managing projects.

## Commit message convention

Clear git commit messages help the code review process and gives you a clear log of what has changed throughout the project. Please read [Chris Beams's article](https://chris.beams.io/posts/git-commit/) to master the best practice of writing commit messages. Also, for the sake of clarity and to link your work to the corresponding Jira ticket, prefix your commit messages with the ticket number you're working on (e.g., APC-42 Integrate analytics platform).
