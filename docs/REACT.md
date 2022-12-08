## 1. Naming

### 1-1. Rules
1. File - `PascalCase`.tsx
2. Class - `PascalCase`
3. Enum - `PascalCase`
  1. Enum values - `UPPER_SNAKE_CASE`
     ```ts
     enum DoobooScreen { IOS, ANDROID, HELLO_WORLD, TEST_DEVICE }
     ```
4. Constants - `UPPER_SNAKE_CASE`
5. Object, classes, variables and functions - `camelCase`
6. Asset file name - `lower_snake_case`.png

### 1-2 Prefix & Suffix

#### 1-2-1. Use prefix to the filenames and components if there is a domain
Put domain as a prefix to filename.
```ts
// Do
CompanyAdd.tsx
CompanyEdit.tsx

// Don't
AddCompany.tsx
EditCompany.tsx
```

For component,
```ts
// Do
function CompanyAdd() {}
function CompanyEdit() {}
```

#### 1-2-2. Use suffixes to functions if there is a domain
```ts
// Do
const onAddCompany = () => {}
const onDeleteCompany = () => {}

// Don't
const onCompanyAdd = () => {}
const onCompanyDelete = () => {}
```

#### 1-2-3. Choose the right boolean prefix
Usually, developers put all boolean prefix with `is` and it often causes confusion. For example below.

```ts
const isFriend = () => user.friends.length > 0;
```

Above code is not quite readable. It'd be better to choose the right prefix. In this case, `hasFried` makes sense.

There are a few other prefixes you can choose to write instead of "is". Below are other options we can recommend.
* has
* should

Also, we prefer avoiding prefixes in boolean if it looks clear enough to stand by themselves. For examples, we think `loading`, `disabled`, `checked` are better without prefixes.

#### 1-2-4. Async suffix
When the function returns `Promise` or is an async function, we prefer adding suffix `Async`.

```ts
// Do
function readAsync() {
  return new Promise();
}

async function readAsync() {
  await fetch();
  return;
}
```

### 1-3. Avoid redundancy

#### 1-3-1. Avoid adding domain to function when it is predictable

```ts
class User {
  // Don't
  void addUser() {
  }

  // Do
  void add() {
  }

  void addMessage() {
  }
}
```


## 2. Folder structures

### 2-1. Assets
#### 2-1-1. Separate assets from the sourcecode
Assets are all kinds of resources used in applications. They should not stay at the same level with sourcecode.

```
YourApp/
├─ assets
│  └─ icons
│  └─ images
│  └─ localizations
├─ src/
```

#### 2-1-2. Separate the config files
Config files should be separated from the sourcecode.

```
YourApp/
├─ src/
├─ .buckconfig
├─ .flowconfig
├─ .gitattributes
├─ .gitignore
├─ .watchmanconfig
├─ app.json
├─ babel.config.js
├─ index.js
├─ jest.config.js
├─ package.json
├─ README.md
├─ tsconfig.json
└─ eslint.config.js
```
> The config files for jest, babel, eslint, typescript, flow, git are all separated from the `src` directory.

### 2-2. Tests
#### 2-2-1. Test extension files in `test` dir
```
YourApp/
├─ src/
│  └─ components/
│     └─ pages/
│        └─ HelloWorld.tsx
│     └─ uis/
│  └─ providers/
├─ test/
│  └─ components/
│     └─ pages
│        └─ HelloWorld.test.tsx
```
> Test files should all lie under `test` dir.

#### 2-2-2. Unit tests can be included in `src`
```
YourApp/
├─ src/
│  └─ utils/
│     └─ localize.ts
│     └─ localize.spec.ts <=== Unit test may stay along with the original file
```

#### 2-2-3. Mock files in `__mocks__` dir
```
YourApp/
├─ __mocks__/
│  └─ reanimated-masonry-list.ts
```

## 3. Code Style

### 3-1. Import

#### 3-1-1. Follows the [dooboolab eslint](https://github.com/dooboolab/eslint-config) package

Follow the import rules of `@dooboo/eslint-config-react` for React and `@dooboo/eslint-config-react-native` for React Native.

#### 3-1-2. Manage asset paths in one place

If you don't manage your asset paths, you'll often face problems like the one below.
```sh
Module not found: Error: Can't resolve '../icons/btn_add'
Module not found: Error: Can't resolve '../../icons/btn_back'
Module not found: Error: Can't resolve '../../../icons/camera'
```

Instead, we recommend organizing assets in one file like below and export them.
```ts
import icAddW from '../../assets/icons/btn_add.png';
import icBack from '../../assets/icons/btn_back.png';
import icCamera from '../../assets/icons/camera.png';
import icCircleX from '../../assets/icons/x_circle.png';

export const IC_ADD_W = icAddW;
export const IC_BACK = icBack;
export const IC_CAMERA = icCamera;
export const IC_CIRCLE_X = icCircleX;
```

### 3-2. Function

#### 3-2-1. Function needs return type
```tsx
// Don't
function Page({}: Props) {}

// Do
function Page({}: Props): ReactElement {}
```

#### 3-2-2. Function component without arrow function

```tsx
// Don't
const Page = () => {};
class Page extends ReactComponent {}

// Do
function Page({}: Props): ReactElement {}
```

### 3-3. Style props
#### 3-3-1. All style props in `styles` and style of current component in `style`
```tsx
type Styles = {
  container?: StyleProp<ViewStyle>;
  text?: StyleProp<TextStyle>;
  disabledButton?: StyleProp<ViewStyle>;
  disabledText?: StyleProp<TextStyle>;
  hovered?: StyleProp<ViewStyle>;
};

function Button({
  styles,
  style,
  ...
});
```
  > You can see the example in [Button L62-L63](https://github.com/dooboolab/dooboo-ui/blob/c0d114c46bbba5a8a8a05e91a782a682e4803dac/main/Button/index.tsx#L62-L63).
  > This lets developers check what kind of styles are nested in the component. It is also easy to handle reusable component's style without knowing what styles are nested with the `style` prop. The user would want to change its `margin` or `padding`.

### 3-4. Conditional statements
#### 3-4-1. Guard and early return

```ts
// Don't
if (checkStatus(user) === 'busy') {
  message = 'User is busy!';
} else if (checkStatus(user) !== 'busy') {
  message = 'User is not busy!';

  if (availableForCall(user)) {
    call("010-xxxx-xxxx");
  } else {
    call("119");
  }
}

// Do
if (checkStatus(user) !== 'busy') {
  message = 'User is not busy!';
  availableForCall(user) ? call('010-xxxx-xxxx') : call('119');
  return;
}

message = 'User is busy';
```
> [Replace nested conditional with guard clauses](https://refactoring.guru/replace-nested-conditional-with-guard-clauses) makes your statements more concise and readable.

#### 3-4-2. Simplify duplicate function call
```ts
// Don't
availableForCall(user) ? call('010-xxxx-xxxx') : call('119');

// Do
call(availableForCall(user) ? '010-xxxx-xxxx' : '119');
```
> Try to remove the duplication function calls if possible.

#### 3-4-3. Table Driven method
Table driven method helps you build better software with decision tables in place of `if-else`.
```ts
// Don't
public string GetMontName(int month, string language) {
  if (month == 1 && language == "EN") {
    return "January";
  } else if (month == 1 && language == "DA") {
    return "Januar";
  } else if (month == 1 && language == "KO") {
    return "1월"
  } else if (month == 2 && language == "EN") {
    return "Feburary";
  } else if (month == 2 && language == "DA") {
    return "Feburar";
  } else if (month == 2 && language == "KO") {
    return "2월";
  } else {
    return "Unknown";
  }
}

// Do
const monthDictionary = {
  "EN": {
    1: "January",
    2: "Faburary",
  },
  "DA": {
    1: "Januar",
    2: "Feburar",
  },
  "KO": {
    1: "1월",
    2: "2월",
  },
}
public string GetMonthName(string language, int month) => monthDictionary[language][month];
```
> Refer to [article](https://medium.com/swlh/replacing-logical-statements-with-table-driven-methods-da1114512134).

#### 3-4-4. Extract function
Extracting function is often used to make code more readable.
```ts
// Don't
const checkString = (str: string) => {
  let subString = string.slice();
  let counter = 0;

  while (subString !== '') {
    counter++;
    subString = subString.slice(1);
  }

  setString(`${str}: ${counter}`); 
}

// Do
function getStringLengthWithoutLengthProperty(string) {
  let subString = string.slice();
  let counter = 0;

  while (subString !== '') {
    counter++;
    subString = subString.slice(1);
  }

  return counter;
}

const checkString = (str: string) => {
  const strLength = getStringLengthWithoutLengthProperty(str);

  setString(`${str}: ${counter}`); 
}

// Do
const setYoutubeVideo = async (url: string): Promise<void> => {
  if (urlType === 'youtube') {
  const videoURL = await getYouTubeDetails(url);

    if (videoURL) {
      setYoutubeURL(videoURL);
    }
  }
};

setYoutubeVideo(url);
```
> In the above `if (str.length % 2)` code, the intention of the code is unknown until the developer who is new to the code dissects the logic. In this case, you need to extract the function and make it more meaningful. Also, [youtube video](https://www.youtube.com/watch?v=0GiyKv6ozP8) tells how to extract functions from vscode more easily.

### 3-5. Component

#### 3-5-1. No arrow function
```tsx
// Do
function Page({}: Props): ReactElement {
  return (
    <Container>
      <Text>Page</Text>
    </Container>
  );
};
```
> Also, see [templates in dooboo-cli](https://github.com/dooboolab/dooboo-cli/blob/main/templates/react-native/templates/Template.tsx).

#### 3-5-2. No `&&` operator when rendering
```tsx
// don't
{list.length && <div>{list.map((item) => (...))}</div>}

// do
{list.length ? <div>{list.map((item) => (...))}</div> : null}
```
> Find out more [in the link](https://kentcdodds.com/blog/use-ternaries-rather-than-and-and-in-jsx) for the decision.

#### 3-5-3. Extract duplicate conditions in props
If there are same conditions used multiple times inside props, extract them to conditional statement like below.
```tsx
// Don't
function Parent() {
  const [isHighlighted, setIsHighlighted] = useState(false);

  return (
  <div>
    <Child
      style={...{isHighlighted && {color: '#fff'}}}
      headerColor={isHighlighted ? 'red' : 'blue'}
    />
  </div>
  );
}

// Do
function Parent() {
  const [isHighlighted, setIsHighlighted] = useState(false);

  if(isHighlighted) {
    return (
      <div>
        <Child
          style={{color: '#fff'}}
          headerColor='red'
        />
      </div>
    )
  }

  return (
    <div>
      <Child headerColor='blue' />
    </div>
  );
}
```
> Here, `isHighlighted` is used more than once in `Child` component props. Extracting them makes it more maintainable because we can concentrate on the target code instead of the side effect.
> Also note that using `&&` operator is good in props like `{isHighlighted && {color: '#fff'}` unlike **[3-5-2](#3-5-2-no--operator-when-rendering)**.

#### 3-5-4. Concise and flexible
Try to make props as simple as possible but flexible. You can able to achieve this by justifying minimal requirements and handing over authority to users if more specs are required.
For example, below `Button` has type `loading` and `disabled` and have `title` and `onPress` function.
```tsx
type Props = {
  type?: 'loading' | 'disabled' | undefined,
  onPress?: () => {}
  title: string
}

function Button({
  type,
  onPress,
  title,
}: Props): ReactElement {
  if (type === 'loading') {
    return ...
  }

  if (type === 'disabled') {
    return ...
  }

  return (
    <TouchableOpacity onPress={onPress}>
      <Text>{title}</Text>
    </TouchableOpacity>
  );
};
```
If the above code is the minimal requirement to use in the product, keep it the way it is and expose the possibility with the render callback function instead of exposing more and more props.

```tsx
type Props = {
  type?: 'loading' | 'disabled' | undefined,
  onPress?: () => {}
  render?: (type: 'loading' | 'disabled' | undefined) => ReactElement
  title: string
}

function Button({
  type,
  onPress,
  title,
  onPress
}: Props): ReactElement {
  if (type === 'loading') {
    return ...
  }

  if (type === 'disabled') {
    return ...
  }

  return (
    <TouchableOpacity onPress={onPress}>
      {render?.(type) || <Text>{title}</Text>}
    </TouchableOpacity>
  );
};
```
> Current habits will help developers prevent producing abstruse components as the possibility grows.

#### 3-5-5. Avoid adding a single property to style props
```tsx
// Do
<Button textStyle={textStyle} />

// Don't
<Button textColor={textColor} />
```
> Exposing a single attribute in style props leads to creating not only complicated but also unmaintainable components. Example [flutter_calendar_carousel](https://github.com/dooboolab/flutter_calendar_carousel).


#### 3-5-6. Avoid using the ternary operator with multiple conditions
If [ternary operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator) is used multiple times when rendering, it hurts <span style="color: #00D9D5">readability</span>.

```tsx
function Button({
  type,
  onPress,
  title,
  onPress
}: Props): ReactElement {
    // Don't
    return type === 'loading' ? ... : type === 'disabled' ? ... : return ...

    // Do
    // 1. If statement
    if (type === 'loading') return ...;
    if (type === 'disabled') return ...;
    return ...;

    // 2. Switch statement
    switch (type) {
        case 'loading':
          return ...
      case 'disabled':
        return ...
      default:
        return ...
    }
    
    // 3. Good with single condition
    return loading ? ... : return ...
}
```

#### 3-5-7. Rest Props
Use the `rest` term when extracting the rest of the props with the spread operator as shown below.
This is to unify communication by limiting terms that can be used in various ways such as `otherProps, restProps`.

```tsx
function Button({
  type,
  onPress,
  title,
  onPress
  ...rest,
}: Props): ReactElement {
```

### 3-6. Reusable Component
#### 3-6-1. Block execution
Never abstract the business logic in a reusable component. Deliver them to the business manager.
```tsx
// Don't
function Button(): ReactElement {
  return (
    // Don't
    <TouchableOpacity onPress={() => {
      console.log('Hello I am doing something!!!');
    }}>
      {render?.(type) || <Text>{title}</Text>}
    </TouchableOpacity>
  );
};

// Do
function Button(): ReactElement {
  return (
    // Don't
    <TouchableOpacity onPress={onPress}>
      {render?.(type) || <Text>{title}</Text>}
    </TouchableOpacity>
  );
};
```
<img src="https://github.com/hyochan/code-convention/blob/main/assets/4-1.%20no%20direct%20run.png?raw=true" width="420"/>

<img src="https://github.com/hyochan/code-convention/blob/main/assets/4-1.%20yes%20callback.png" width="420"/>

> When UI components have multiple nested child components, try refactoring with provider. We prefer [recoil](https://recoiljs.org) in this case.


### 3-7. Types

#### 3-7-1. Prefer using `type` instead of `interface`
```ts
// Don't => But only when needed
interface Props {}

// Do
type Props = {}
```

#### 3-7-2. Don't inject type when it can be inferred
```ts
// Don't
const name = useState<string>('');

// Do
const name = useState('');
```
