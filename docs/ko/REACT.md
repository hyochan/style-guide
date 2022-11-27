## 1. 네이밍

### 1-1. 규칙
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

### 1-2 Prefix와 Suffix

#### 1-2-1. 도메인이 있는 경우 파일 이름 및 구성 요소에 prefix 사용
파일 이름에 도메인을 접두사로 추가합니다.

```ts
// Do
CompanyAdd.tsx
CompanyEdit.tsx

// Don't
AddCompany.tsx
EditCompany.tsx
```

구성 요소의 경우 아래와 같이 이름을 지정합니다.

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

#### 1-2-3. 올바른 boolean prefix 사용
일반적으로 많은 개발자들이 모든 부울 접두사를 `is`로 지정하며 종종 혼란을 야기합니다. 아래 코드를 예로 들어보겠습니다.

```ts
const isFriend = () => user.friends.length > 0;
```

위의 코드는 가독성이 좋지 않습니다. 올바른 접두사를 사용하지 않았기 때문입니다. 위 경우 친구가 있는지 확인하는 변수이기 때문에 `hasFriend`로 바꾸는 것이 좋습니다.

`is` 대신 쓸 수 있는 몇 가지 다른 접두사가 있습니다. 다음은 권장할 수 있는 다른 옵션입니다.
* has
* should

또한 부울 접두사 자체가 명확해 보이면 접두사를 피하는 것이 좋습니다. 예를 들어 접두사가 없는 `loading`, `disabled`, `checked`가 더 좋다고 생각합니다.


#### 1-2-4. Async suffix

함수가 `Promise`를 반환하거나 비동기 함수인 경우 접미사 `Async`를 추가하는 것을 권장합니다.

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

### 1-3. 불필요함 방지

#### 1-3-1. 예측 가능한 함수에 도메인 제외
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

## 2. 폴더 구조

### 2-1. Assets
### 2-1-1. Asset을 소스코드와 분리
자산은 애플리케이션에서 사용되는 모든 종류의 리소스입니다. 그들은 소스 코드와 같은 수준에 머물러서는 안됩니다.

```
YourApp/
├─ assets
│  └─ icons
│  └─ images
│  └─ localizations
├─ src/
```

### 2-1-2. 설정 파일을 소소코드로부터 분리
구성 파일은 소스 코드와 분리되어야 합니다.

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
> jest, babel, eslint, typescript, flow, git에 대한 구성 파일은 모두 `src` 디렉토리에서 분리됩니다.

### 2-2. Tests
### 2-2-1. 테스트 확장 파일은 `test` 디렉토리에 포함
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
> 테스트 파일은 모두 `test` 디렉토리 아래에 있어야 합니다.

### 2-2-2. Unit test는 `src` 디렉토리에 포함되어도 무방
```
YourApp/
├─ src/
│  └─ utils/
│     └─ localize.ts
│     └─ localize.spec.ts <=== Unit test may stays along with original file
```

### 2-2-3. Mock files in `__mocks__` dir
```
YourApp/
├─ __mocks__/
│  └─ reanimated-masonry-list.ts
```

## 3. 코드 스타일

### 3-1. Import

#### 3-1-1. dooboolab eslint 패키지를 따름

React의 경우 `@dooboo/eslint-config-react`, React Native의 경우 `@dooboo/eslint-config-react-native`의 import 규칙을 따릅니다.

### 3-2. 함수

#### 3-2-1. 함수에 리턴타입을 명시

```tsx
// Don't
function Page({}: Props) {}

// Do
function Page({}: Props): ReactElement {}
```

### 3-2-2. 화살표가 없는 함수 컴포넌트

```tsx
// Don't
const Page = () => {};
class Page extends ReactComponent {}

// Do
function Page({}: Props): ReactElement {}
```

### 3-3. 스타일 프롭스

#### 3-3-1. 모든 스타일 프롭스는 `styles`에 그리고 현 컴포넌트의 스타일은 `style`에 담기

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
  > [Button L62-L63](https://github.com/dooboolab/dooboo-ui/blob/c0d114c46bbba5a8a8a05e91a782a682e4803dac/main/Button/index.tsx#L62-L63)에서 예제를 볼 수 있습니다.
  > 이를 통해 개발자는 구성 요소에 어떤 종류의 스타일이 중첩되어 있는지 확인할 수 있습니다. 또한 어떤 스타일이 `style` prop과 중첩되어 있는지 몰라도 재사용 가능한 컴포넌트의 스타일을 쉽게 처리할 수 있습니다. 사용자는 `margin` 또는 `padding`을 변경하려고 합니다.

### 3-4. 조건문

### 3-4-1. 가드와 조기 리턴

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
  message = 'User is busy!';
  availableForCall(user) ? call('010-xxxx-xxxx') : call('119');
  return;
}

message = 'User is not busy';

```
> [가드 절로 중첩된 조건문 교체](https://refactoring.guru/replace-nested-conditional-with-guard-clauses)를 사용하면 문장이 더 간결하고 읽기 쉬워집니다.

### 3-4-2. 중복 함수 호출 단순화

```ts
// Don't
availableForCall(user) ? call('010-xxxx-xxxx') : call('119');

// Do
call(availableForCall(user) ? '010-xxxx-xxxx' || '119');
```
> 가능하면 중복 함수 호출을 제거하십시오.

### 3-4-3. 테이블 기반 메소드

테이블 기반 방법은 `if-else` 대신 결정 테이블을 사용하여 더 나은 소프트웨어를 구축하도록 도와줍니다.

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

### 3-4-4. Extract function
Extracting function is often used to make code more readable.
```ts
// Don't
const checkString = (str: string) => {
  if (str.length % 2 === 0) {
    str.slice(str.length / 2, str.length);
  }

  setString(str); 
}

// Do
const extractStr = (str: string) => {
  if (str.length % 2 === 0) {
    return str.slice(str.length / 2, str.length);
  }

  return str;
}

const checkString = (str: string) => {
  const extractedStr = extractStr(str);

  setString(str); 
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
> It is ambiguous to know what the developer aims for when writing `if (str.length % 2)` until newcomers dissect the logic. Try to extract the function and make it more meaninful. Also, the [youtube video](https://www.youtube.com/watch?v=0GiyKv6ozP8) teaches you how to extract the function in vscode more easily.


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
    <Child
    headerColor='blue'
    />
  </div>
  );
}
```
> Here, `isHighlighted` is used more than one in `Child` component props. Extracting them makes it more maintainable because we can concentrate on the target code instead of the side effect.
> Also note that using `&&` operator is good in props like `{isHighlighted && {color: '#fff'}` unlike **[1-2]()**.

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
If above code is the minimal requirement to use in the product, keep it the way it is and expose possibility with render function instead of exposing more and more props.
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
> 테이블 기반 방법론은 `if-else` 대신 의사결정 테이블을 사용하여 더 나은 소프트웨어를 구축하도록 도와줍니다.

#### 3-5-5. 스타일 프롭스에 단일 속성 추가 방지
```tsx
// Do
<Button textStyle={textStyle} />

// Don't
<Button textColor={textColor} />
```
> 스타일 프롭스에 단일 속성을 노출하면 복잡할 뿐만 아니라 구성 요소들을 유지 관리하기 어려워집니다. 예 [flutter_calendar_carousel](https://github.com/dooboolab/flutter_calendar_carousel).


#### 3-5-6. 여러 조건에 삼항 연산자 사용 방지
렌더링 시 [삼항 연산자](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Conditional_Operator)를 여러 번 사용하면 <span style="color: #00D9D5 ">가독성</span>이 떨어집니다.
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

### 3-6. 재사용 컴포넌트
#### 3-6-1. 논리를 직접 실행 차단
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

> UI 구성 요소에 중첩된 자식 구성 요소가 여러 개 있는 경우 공급자를 사용하여 리팩터링을 시도하십시오. 이 경우 [recoil](https://recoiljs.org)을 선호합니다.


### 3-7. 타입

### 3-7-1. `Interface` 대신 `Type` 사용 선호
```ts
// Don't => But only when needed
interface Props {}

// Do
type Props = {}
```

### 3-7-2. 유추할 수 있는 타입 주입 방지
```ts
// Don't
const name = useState<string>('');

// Do
const name = useState('');
```
