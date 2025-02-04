# dart-cp949 (NULL SAFE)

CP949 을 Encode, Decode 해 주는 라이브러리입니다.
(CP949 는 EUC-KR 의 상위집합이기 때문에, EUC-KR 도 다룰 수 있습니다.)

[![license](https://img.shields.io/badge/license-MIT-ff4081.svg?style=flat-square&labelColor=black)](./LICENSE)

## Usage

### `decode(List<int> codeUnits) -> String`

CP949 (EUC-KR) byte 배열을 (`List<int>`) 받아 dart 의 native String 을 리턴합니다.

```dart
import 'package:cp949/cp949.dart' as cp949;

const cp949CodeUnitBytes = [0xBE, 0xC6, 0xB8, 0xA7, 0xB4, 0xD9, 0xbf, 0xee];
final decoded = cp949.decode(cp949CodeUnitBytes)); // '아름다운'

const brokenString = 'ÄÁÅÙÃ÷';
final decoded = cp949.decode(brokenString.codeUnits); // '컨텐츠'
```

### `decodeString(String brokenString) -> String`

CP949 (EUC-KR) byte 배열을 유니코드 기반으로 잘못 해석하여 깨져 보이는 String 을 받아 변환해 제대로 리턴합니다.  
(dart 에서 깨져 보이는 것 뿐이지, 실 데이터 유실은 없습니다.)

```dart
import 'package:cp949/cp949.dart' as cp949;

const brokenString = 'ÄÁÅÙÃ÷';
final decoded = cp949.decodeString(brokenString); // '컨텐츠'
```

### `encode(String string) -> List<int>`

dart 의 native String 을 받아 CP949 (EUC-KR) byte 배열로 (`List<int>`) 리턴합니다.

```dart
import 'package:cp949/cp949.dart' as cp949;

final encoded = cp949.encode('아름다운'); // [0xBE, 0xC6, 0xB8, 0xA7, 0xB4, 0xD9, 0xBF, 0xEE]

final encoded = String.fromCharCodes(cp949.encode('컨텐츠')); // 'ÄÁÅÙÃ÷' (제대로 된 결과입니다!)
```

### `encodeToString(String string) -> String`

CP949 (EUC-KR) byte 배열을 유니코드 기반으로 잘못 해석하여 깨져 보이는 String 을 리턴합니다.  
(dart 에서만 깨져보이는 것이지, 제대로 인코딩 된 것이므로, CP949 (EUC-KR) 을 처리할 수 있는 다른 프로그램으로 보낼 시 (REST API 등) 호환됩니다.)

```dart
import 'package:cp949/cp949.dart' as cp949;

final encoded = cp949.encodeToString('컨텐츠'); // 'ÄÁÅÙÃ÷' (제대로 된 결과입니다!)
```

## Examples

EUC-KR 로 인코딩된 http 응답을 받아오는 예시를 들면 다음과 같습니다.

```dart
import 'package:http/http.dart' as http;
import 'package:cp949/cp949.dart' as cp949;

const url = 'https://euc-kr-encoded-website.co.kr';
final response = await http.get(url);
print(cp949.decode(response.bodyBytes));
// 또는 print(cp949.decode(response.body.codeUnits));
// 또는 print(cp949.decodeString(response.body));
```

EUC-KR 인코딩을 사용하는 REST API 를 사용하는 예시를 들면 다음과 같습니다.

```dart
import 'package:http/http.dart' as http;
import 'package:cp949/cp949.dart' as cp949;

const url = 'https://euc-kr-accepting-api.co.kr';
await http.post(url,
  body: {'title': cp949.encodeToString('컨텐츠'), 'foo': 'bar'});
```

## Development (Contribution)

이 project 는 마크다운 린트, 커밋 메세지 린트 등 개발환경 설정을 위해 yarn과 nodejs package 들을 사용합니다.

```shell
yarn install
yarn test
yarn format:md .
yarn format .
```

## License

[MIT License](LICENSE). Copyright &copy; 2020, GIL B. Chan <[bnbcmindnpass@gmail.com](mailto:bnbcmindnpass@gmail.com)>
