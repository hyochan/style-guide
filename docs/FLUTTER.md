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

## 2. 폴더 구조

## 3. 코드 스타일

### 3-1. Import 문

#### 3-1-1. 현재 제품의 패키지는 `show`를 붙입니다.
현재 어플리케이션 패키지는 `show`를 되도록 사용해줍니다. 다른 패키지들은 사용하지 않습니다.

```dart
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:flat_list/flat_list.dart';
import 'package:flutter/material.dart';
import 'package:flutter_hooks/flutter_hooks.dart';
import 'package:provider/provider.dart';

import 'package:kingtalk/models/company_model.dart' show CompanyModel;
import 'package:kingtalk/models/discussion_model.dart' show DiscussionModel;
import 'package:kingtalk/utils/navigation.dart' show navigation;
import 'package:kingtalk/utils/routes.dart' show AppRoute, RouteName;
import 'package:kingtalk/widgets/common/loading_indicator.dart'
    show LoadingIndicator;
import 'package:kingtalk/widgets/common/styles.dart'
    show NavigationTitleTextStyle;
...
```

### 3-2. Grouping

#### 3-2-1. 도메인 별로 파일 생성 및 클래스 묶기
`discussion_provider.dart` 파일 안에 `MyCompaniesDiscussionsState`, `CompaniesDiscussionsState` 들을 관리할 수 있습니다. 이는 같은 도메인에 대한 로직을 수행하므로 한 파일에서도 충분히 알아볼 수 있습니다.

#### 3-2-2. 스크린이나 재사용 위젯은 한 파일에 하나만
유틸리티성 코드들은 묶어서 한 파일로 제공하는 것이 관리하기 편할 수도 있으나 위젯들은 그렇지 않습니다.
다만 [UserListItem]과 [UserContainerItem]이 존재할 경우에는 [UserContainerItem]이 UI를 위해 존재하는 것이 아니기에 `user_list_item.dart` 파일이 들어가도 무방합니다. 가급적 위젯은 파일을 분류해서 작업합니다.

### 3-3. Singleton

#### 3-3-1. Singleton or single instance 자유롭게 사용
싱글톤을 쓸지 싱글 인스턴스를 쓸지는 정하지 않습니다만 Firestore같은 경우 single instance를 사용하고 있습니다.
따라서 우리도 네트워크 관련된 코드를 작성할 때는 single instance를 사용합니다.

### 3-4. FutureBuilder

#### 3-4-1. UI 렌더링 시점에서 future 데이터가 필요한 경우 사용

우선 아래 코드를 보겠습니다.
```dart
var discussion = item as DiscussionModel;

return FlatList(
	buildItem: (item, index) {
		var company = await CompanyRepository.instance.getOne();
	
		return CompanyListItem(
		...
```
> 위 코드에서는 오류가 발생합니다. UI를 그리는 시점에서 async 함수를 호출하기 때문입니다. [Widget build] 메소드는 `async`가 될 수 없습니다. 따라서 아래와 같은 lint error도 확인할 수 있습니다. 	

```sh
The await expression can only be used in an async function.  
	Try marking the function body with either 'async' or 'async*'.dart(await_in_wrong_context)
```

다음과 같이 수정합니다.
```dart
return FutureBuilder(
	future: CompanyRepository.instance.getOne(),
	builder: (context, snapshot) {

	if (snapshot.hasData) {
		var company = snapshot.data;
	
		return CompanyListItem(
		...
```
> 위와 같이 UI 렌더링 시점에서 다른 데이터를 필요로 할 때만 사용합니다.
> 
> 최상위에서 사용할 경우 매번 state가 변경될 때마다 전체 화면이 릴로딩 되므로 사용을 하지 말아주세요.


### 3-5. StreamBuilder
Stream은 굉장히 fragile하므로 초심자가 다루기 어렵습니다. 따라서 익숙하지 않으면 사용을 피해주세요.

#### 3-5-1. Infinite scroll 등 pagination 용으로는 사용하지 않음
구독하는 stream을 새로운 리스트를 불러올 때마다 업데이트 해줘야하는데 스테이트 복잡성이 늘어납니다.

### 3-6. FlatList
#### 3-6-1. Overfetching을 방지
Overfetching을 방지하기 위해 마지막 리스트 아이디 상태 변수를 관리해주세요.
```dart
var lastListIdState = useState<String?>(null);

Future<void> fetchNext(String startAfter) async {
  if (lastListIdState.value == startAfter) return;
```


### 3-7. Provider

#### 3-7-1. provider 변수 선언 및 사용

```dart
var companiesDiscussionState =
	Provider.of<CompaniesDiscussionsState>(context, listen: false);
var myCompaniesDiscussionState =
	Provider.of<MyCompaniesDiscussionsState>(context, listen: false);
final titleController = useTextEditingController(text: '');
final contentController = useTextEditingController(text: '');
final contentSize = useState(0);
final urlController = useTextEditingController();
final loading = useState(false);
```

위와 같이 provider 변수들은 필요할 때 바로 사용하지 않고 미리 선언해서 사용합니다. 이는 현재 Widget에서 어떤 provider 변수들을 거시적으로 보기 위함입니다. !사용하지 않으면 삭제해줍니다.
