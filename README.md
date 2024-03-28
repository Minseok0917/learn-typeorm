[validatorOptions]

```typescript
export interface ValidatorOptions {
  skipMissingProperties?: boolean; // 누락된 속성을 검증에서 건너뛸지 여부를 결정합니다.
  whitelist?: boolean; // 화이트리스트 모드를 활성화하여 정의된 데코레이터가 없는 모든 속성을 제거합니다.
  forbidNonWhitelisted?: boolean; // 화이트리스트에 없는 속성이 있을 경우 검증을 실패하게 합니다.
  groups?: string[]; // 특정 검증 그룹을 지정합니다. 이 옵션을 사용하면 지정된 그룹에만 속한 데코레이터가 적용됩니다.
  dismissDefaultMessages?: boolean; // 기본 오류 메시지를 무시하고 사용자 정의 메시지만 반환합니다.
  validationError?: {
    target?: boolean; // 오류가 발생한 객체를 `ValidationError`에 포함시킬지 여부를 결정합니다.
    value?: boolean; // 검증 실패한 값 자체를 오류에 포함할지 여부를 결정합니다.
  };
  forbidUnknownValues?: boolean; // 알려지지 않은 객체가 검증을 통과하는 것을 금지합니다. 기본값은 `true`입니다.
  stopAtFirstError?: boolean; // 첫 번째 검증 오류 발생 시 나머지 검증을 중단합니다.
}
```

[validatorError]

```typescript
export interface ValidatorError {
  target: Object; // 검증된 객체입니다.
  property: string; // 검증에 실패한 객체의 속성입니다.
  value: any; // 검증에 실패한 값입니다.
  constraints?: {
    // 오류 메시지와 함께 검증에 실패한 제약 조건들입니다.
    [type: string]: string;
  };
  children?: ValidationError[]; // 해당 속성의 모든 중첩된 검증 오류들을 포함합니다.
}
```

**exmaple**

```typescript
import {
  validate,
  validateOrReject,
  Contains,
  IsInt,
  Length,
  IsEmail,
  IsFQDN,
  IsDate,
  Min,
  Max,
} from "class-validator";

export class Post {
  @Length(10, 20)
  title: string;

  @Contains("hello")
  text: string;

  @IsInt()
  @Min(0)
  @Max(10)
  rating: number;

  @IsEmail()
  email: string;

  @IsFQDN()
  site: string;

  @IsDate()
  createDate: Date;
}

let post = new Post();
post.title = "Hello"; // should not pass
post.text = "this is a great post about hell world"; // should not pass
post.rating = 11; // should not pass
post.email = "google.com"; // should not pass
post.site = "googlecom"; // should not pass

validator.validate(post, { validationError: { target: false } });
```

```typescript
[{
    target: /* post 객체 */, // 검증된 대상 객체
    property: "title", // 검증에 실패한 속성
    value: "Hello", // 검증에 실패한 값
    constraints: {
        length: "속성은 10자 이상이어야 합니다" // 검증 실패 제약 조건과 오류 메시지
    }
}, {
    target: /* post 객체 */, // 검증된 대상 객체
    property: "text", // 검증에 실패한 속성
    value: "this is a great post about hell world", // 검증에 실패한 값
    constraints: {
        contains: "텍스트는 'hello' 문자열을 포함해야 합니다" // 검증 실패 제약 조건과 오류 메시지
    }
},
// 그 외 오류들
]
```

[Javascript validation decorators]

- `@ValidateNested()` : 객체가 중첩된 객체를 포함하고 있고 검증기가 그 중첩된 객체의 검증도 수행하기를 원한다면 사용합니다. 중첩된 객체는 반드시 클래스의 인스턴스여야 합니다.
- `@ValidatePromise()` : @ValidatePromise() 객체의 속성이 Promise를 반환하고 그 Promise의 해결된 값이 검증되어야 한다면 사용합니다. 이 데코레이터는 Promise가 해결된 후, 그 값에 대한 검증을 수행합니다.
- `@ValidateIf((value) => boolean)` : @ValidateIf((o) => boolean) 제공된 조건 함수가 false를 반환할 때 해당 속성에 대한 검증자를 무시합니다. 조건 함수는 검증되고 있는 객체를 받아 boolean을 반환해야 합니다.

[Common validation decorators]

- `@IsDefined(value: any)` : 값이 정의되었는지 확인합니다 (!== undefined, !== null). 이 데코레이터는 skipMissingProperties 옵션을 무시하는 유일한 데코레이터입니다.
- `@IsOptional()` : 주어진 값이 비어 있는지 확인합니다 (=== null, === undefined) 그리고 그렇다면, 해당 속성에 대한 모든 검증자를 무시합니다.
- `@Equals(comparison: any)` : 값이 주어진 비교 대상과 동일한지 확인합니다 (===).
- `@NotEquals(comparison: any)` : 값이 주어진 비교 대상과 다른지 확인합니다 (!==).
- `@IsEmpty()` : 주어진 값이 비어 있는지 확인합니다 (=== '', === null, === undefined).
- `@IsNotEmpty()` : 주어진 값이 비어 있지 않은지 확인합니다 (!== '', !== null, !== undefined).
- `@IsIn(values: any[])` : 값이 허용된 값들의 배열에 포함되어 있는지 확인합니다.
- `@IsNotIn(values: any[])` : 값이 금지된 값들의 배열에 포함되어 있지 않은지 확인합니다.

[Type validation decorators]

- `@IsBoolean()` : 값이 불리언인지 확인합니다.
- `@IsDate()` : 값이 날짜인지 확인합니다.
- `@IsString()` : 값이 문자열인지 확인합니다.
- `@IsNumber(options: IsNumberOptions)` : 값이 숫자인지 확인합니다.
- `@IsInt()` : 값이 정수인지 확인합니다.
- `@IsArray()` : 값이 배열인지 확인합니다.
- `@IsEnum(entity: object)` : 값이 유효한 열거형(enum)인지 확인합니다.

[Number validation decorators]

- `@IsDivisibleBy(num: number)` : 값이 다른 수로 나누어 떨어지는 숫자인지 확인합니다.
- `@IsPositive()` : 값이 0보다 큰 양수인지 확인합니다.
- `@IsNegative()` : 값이 0보다 작은 음수인지 확인합니다.
- `@Min(min: number)` : 주어진 수가 주어진 수보다 크거나 같은지 확인합니다.
- `@Max(max: number)` : 주어진 수가 주어진 수보다 작거나 같은지 확인합니다.

[Date validation decorators]

- `@MinDate(date: Date | (() => Date))` : 값이 지정된 날짜 이후인지 확인합니다.
- `@MaxDate(date: Date | (() => Date))` : 값이 지정된 날짜 이전인지 확인합니다.

[String-type validation decorators]

- `@IsBooleanString()` : 문자열이 불리언인지 확인합니다 (예: "true" 또는 "false" 또는 "1", "0").
- `@IsDateString()` : @IsISO8601의 별칭입니다.
- `@IsNumberString(options?: IsNumericOptions)` : 문자열이 숫자인지 확인합니다.

[String validation decorators]

- `@Contains(seed: string)` : 문자열이 지정된 seed를 포함하는지 확인합니다.
- `@NotContains(seed: string)` : 문자열이 지정된 seed를 포함하지 않는지 확인합니다.
- `@IsAlpha()` : 문자열이 오직 글자(a-zA-Z)만 포함하는지 확인합니다.
- `@IsAlphanumeric()` : 문자열이 글자와 숫자만 포함하는지 확인합니다.
- `@IsDecimal(options?: IsDecimalOptions)` : 문자열이 유효한 십진수 값인지 확인합니다. 기본 IsDecimalOptions는 force_decimal=False, decimal_digits: '1,', locale: 'en-US' 입니다.
- `@IsAscii()` : 문자열이 오직 ASCII 문자만 포함하는지 확인합니다.
- `@IsBase32()` : 문자열이 base32로 인코딩되었는지 확인합니다.
- `@IsBase58()` : 문자열이 base58로 인코딩되었는지 확인합니다.
- `@IsBase64(options?: IsBase64Options)` : 문자열이 base64로 인코딩되었는지 확인합니다.
- `@IsIBAN()` : 문자열이 IBAN(국제 은행 계좌 번호)인지 확인합니다.
- `@IsBIC()` : 문자열이 BIC(은행 식별 코드) 또는 SWIFT 코드인지 확인합니다.
- `@IsByteLength(min: number, max?: number)` : 문자열의 길이(바이트 단위)가 특정 범위 내에 있는지 확인합니다.
- `@IsCreditCard()` : 문자열이 신용카드 번호인지 확인합니다.
- `@IsCurrency(options?: IsCurrencyOptions)` : 문자열이 유효한 통화 금액인지 확인합니다.
- `@IsISO4217CurrencyCode()` : 문자열이 ISO 4217 통화 코드인지 확인합니다.
- `@IsEthereumAddress()` : 문자열이 이더리움 주소인지 기본 정규식을 사용하여 확인합니다. 주소 체크섬을 검증하지 않습니다.
- `@IsBtcAddress()` : 문자열이 유효한 BTC 주소인지 확인합니다.
- `@IsDataURI()` : 문자열이 데이터 URI 포맷인지 확인합니다.
- `@IsEmail(options?: IsEmailOptions)` : 문자열이 이메일인지 확인합니다.
- `@IsFQDN(options?: IsFQDNOptions)` : 문자열이 완전한 도메인 이름(e.g. domain.com)인지 확인합니다.
- `@IsFullWidth()` : 문자열에 전체 너비 문자가 포함되어 있는지 확인합니다.
- `@IsHalfWidth()` : 문자열에 반 너비 문자가 포함되어 있는지 확인합니다.
- `@IsVariableWidth()` : 문자열에 전체 너비와 반 너비 문자가 혼합되어 있는지 확인합니다.
- `@IsHexColor()` : 문자열이 16진수 색상인지 확인합니다.
- `@IsHSL()` : 문자열이 HSL 색상인지 CSS Colors Level 4 사양에 기반하여 확인합니다.
- `@IsRgbColor(options?: IsRgbOptions)` : 문자열이 RGB 또는 RGBA 색상인지 확인합니다.
- `@IsIdentityCard(locale?: string)` : 문자열이 특정 국가 코드에 따른 유효한 신분증 코드인지 확인합니다.
- `@IsPassportNumber(countryCode?: string)` : 문자열이 특정 국가 코드에 따른 유효한 여권 번호인지 확인합니다.
- `@IsPostalCode(locale?: string)` : 문자열이 우편 번호인지 확인합니다.
- `@IsHexadecimal()` : 문자열이 16진수인지 확인합니다.
- `@IsOctal()` : 문자열이 8진수인지 확인합니다.
- `@IsMACAddress(options?: IsMACAddressOptions)` : 문자열이 MAC 주소인지 확인합니다.
- `@IsIP(version?: "4"|"6")` : 문자열이 IP 주소(버전 4 또는 6)인지 확인합니다.
- `@IsPort()` : 문자열이 유효한 포트 번호인지 확인합니다.
- `@IsISBN(version?: "10"|"13")` : 문자열이 ISBN(버전 10 또는 13)인지 확인합니다.
- `@IsEAN()` : 문자열이 EAN(유럽 상품 번호)인지 확인합니다.
- `@IsISIN()` : 문자열이 ISIN(증권 식별 번호)인지 확인합니다.
- `@IsISO8601(options?: IsISO8601Options)` : 문자열이 유효한 ISO 8601 날짜 형식인지 확인합니다. 유효한 날짜에 대한 추가 검사를 위해 strict = true 옵션을 사용하세요.
- `@IsJSON()` : 문자열이 유효한 JSON인지 확인합니다.
- `@IsJWT()` : 문자열이 유효한 JWT인지 확인합니다.
- `@IsObject()` : 객체가 유효한 객체인지 확인합니다(널, 함수, 배열은 false를 반환합니다).
- `@IsNotEmptyObject()` : 객체가 비어 있지 않은지 확인합니다.
- `@IsLowercase()` : 문자열이 소문자인지 확인합니다.
- `@IsLatLong()` : 문자열이 유효한 위도-경도 좌표 형식(lat, long)인지 확인합니다.
- `@IsLatitude()` : 문자열 또는 숫자가 유효한 위도 좌표인지 확인합니다.
- `@IsLongitude()` : 문자열 또는 숫자가 유효한 경도 좌표인지 확인합니다.
- `@IsMobilePhone(locale: string)` : 문자열이 특정 국가의 유효한 휴대전화 번호인지 확인합니다.
- `@IsISO31661Alpha2()` : 문자열이 공식적으로 할당된 유효한 ISO 3166-1 알파-2 국가 코드인지 확인합니다.
- `@IsISO31661Alpha3()` : 문자열이 공식적으로 할당된 유효한 ISO 3166-1 알파-3 국가 코드인지 확인합니다.
- `@IsLocale()` : 문자열이 로케일인지 확인합니다.
- `@IsPhoneNumber(region: string)` : 문자열이 libphonenumber-js를 사용하여 특정 지역의 유효한 전화 번호인지 확인합니다.
- `@IsMongoId()` : 문자열이 유효한 MongoDB ObjectId의 16진수 인코딩 표현인지 확인합니다.
- `@IsMultibyte()` : 문자열에 하나 이상의 다바이트 문자가 포함되어 있는지 확인합니다.
- `@IsNumberString(options?: IsNumericOptions)` : 문자열이 숫자 문자열인지 확인합니다.
- `@IsSurrogatePair()` : 문자열에 서로게이트 쌍 문자가 포함되어 있는지 확인합니다.
- `@IsTaxId()` : 문자열이 특정 로케일('en-US'가 기본값)에 대한 유효한 세금 ID인지 확인합니다.
- `@IsUrl(options?: IsURLOptions)` : 문자열이 URL인지 확인합니다.
- `@IsMagnetURI()` : 문자열이 마그넷 URI 형식인지 확인합니다.
- `@IsUUID(version?: UUIDVersion)` : 문자열이 UUID(버전 3, 4, 5 또는 모든 버전)인지 확인합니다.
- `@IsFirebasePushId()` : 문자열이 Firebase Push ID인지 확인합니다.
- `@IsUppercase()` : 문자열이 대문자인지 확인합니다.
- `@Length(min: number, max?: number)` : 문자열의 길이가 주어진 범위 내에 있는지 확인합니다.
- `@MinLength(min: number)` : 문자열의 길이가 주어진 최소 길이 이상인지 확인합니다.
- `@MaxLength(max: number)` : 문자열의 길이가 주어진 최대 길이 이하인지 확인합니다.
- `@Matches(pattern: RegExp, modifiers?: string)` : 문자열이 패턴과 일치하는지 확인합니다. 'matches('foo', /foo/i)' 또는 'matches('foo', 'foo', 'i')' 형식을 사용할 수 있습니다.
- `@IsMilitaryTime()` : 문자열이 군사 시간 형식(HH:MM)의 유효한 표현인지 확인합니다.
- `@IsTimeZone()` : 문자열이 유효한 IANA 시간대를 나타내는지 확인합니다.
- `@IsHash(algorithm: string)` : 문자열이 지정된 알고리즘(md4, md5, sha1, sha256, sha384, sha512, ripemd128, ripemd160, tiger128, tiger160, tiger192, crc32, crc32b)으로 해싱된 값인지 확인합니다.
- `@IsMimeType()` : 문자열이 유효한 MIME 타입 형식과 일치하는지 확인합니다.
- `@IsSemVer()` : 문자열이 시맨틱 버전(SemVer) 사양에 부합하는지 확인합니다.
- `@IsISSN(options?: IsISSNOptions)` : 문자열이 ISSN(국제 표준 직렬 번호)인지 확인합니다.
- `@IsISRC()` : 문자열이 ISRC(국제 표준 녹음 코드)인지 확인합니다.
- `@IsRFC3339()` : 문자열이 유효한 RFC 3339 날짜인지 확인합니다.
- `@IsStrongPassword(options?: IsStrongPasswordOptions)` : 문자열이 강력한 비밀번호인지 확인합니다.

[Array validation decorators]

- `@ArrayContains(values: any[])` : 배열이 주어진 값들을 모두 포함하는지 확인합니다.
- `@ArrayNotContains(values: any[])` : 배열이 주어진 값들 중 어느 것도 포함하지 않는지 확인합니다.
- `@ArrayNotEmpty()` : 주어진 배열이 비어 있지 않은지 확인합니다.
- `@ArrayMinSize(min: number)` : 배열의 길이가 지정된 숫자보다 크거나 같은지 확인합니다.
- `@ArrayMaxSize(max: number)` : 배열의 길이가 지정된 숫자보다 작거나 같은지 확인합니다.
- `@ArrayUnique(identifier?: (o) => any)` : 배열의 모든 값이 유일한지 확인합니다. 객체에 대한 비교는 참조 기반입니다. 비교에 사용될 값을 반환하는 선택적 함수를 지정할 수 있습니다.

[Object validation decorators]

- `@IsInstance(value: any)` : 속성이 전달된 값의 인스턴스인지 확인합니다.

[Other decorators]

- `@Allow()` : 다른 제약 조건이 지정되지 않았을 때 속성이 제거되는 것을 방지합니다.
