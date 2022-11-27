## 1. Naming

### 1-1. Rules
1. File - `lower_camel_case`.dart
2. Class - `PascalCase`
3. Enum - `PascalCase`
  1. Enum values - `camelCase`
    ```dart
    enum DoobooScreen { iOS, android, helloWorld, testDevice }
    ```
4. Constants - `camelCase`
5. Object, classes, variables and functions - `camelCase`
6. Asset file name - `lower_snake_case`.png

### 1-2 Prefix & Suffix

#### 1-2-1. Use prefixes to the filenames and components if there is a domain
Put domain as a prefix to filename.

```dart
// Do
company_add.dart
company_edit.dart

// Don't
add_company.dart
edit_company.dart
```

For widget, name them like below,

```dart
// Do
class CompanyAdd extends HookWidget {}
class CompanyEdit extends HookWidget {}
```

#### 1-2-2. Use suffixes to functions if there is a domain
```dart
// Do
const onAddCompany () {}
const onDeleteCompany () {}

// Don't
const onCompanyAdd () {}
const onCompanyDelete () {}
```

#### 1-2-3. Choose the right boolean prefix
Usually, developers put all boolean prefix with `is` and it often causes confusion. For example below.

```dart
final isFriend = () => user.friends.length > 0;
```
Above code is not quite readable. It'd be better to choose the right prefix. In this case, `hasFried` makes sense.

There are few prefixes you can choose to write instead of "is". Below are other options we can recommend.
* has
* should

Also, we prefer avoiding prefixes in boolean if it looks clear enough to stand by themselves. For examples, we think `loading`, `disabled`, `checked` are better without prefixes.

#### 1-2-4. Async suffix
When function returns `Future` or is an async function, we prefer adding suffix `Async`.

```dart
// Do
function readAsync() async {
  return new Promise();
}

async function readAsync() async {
  await Future([fetch()]);
  return;
}
```

### 1-3. Avoid redundancy

#### 1-3-1. Avoid adding domain to function when it is predictable.

```dart
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

TODO: Need to translate Korean into here.