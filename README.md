#검증
1) 필드오류 처리
- errors?. 는 error가 null일 때 NullPointException 이 발생하는 대신 null을 반환
- 필드오류 처리
```
<div th:if="${errors?.containsKey('globalError')}">
<input type="text" th:classappend="${errors?.containsKey('itemName')} ? 'fielderror' : _"class="form-control">
```

2) BindingResult
- 필드오류-fieldError
```
//public FieldError(String objectName, String field, String defaultMessage) {}
bindingResult.addError(new FieldError("item", "itemName", "상품 이름은 필수입니니다");

```
- 글로벌오류-ObjectError
```
//public ObjectError(String objectName, String defaultMessage) {
bindingResult.addError(new ObjectError("item", "가격 * 수량의 합은 10,000원 이상이어야합니다. 현재 값 = " + resultPrice));
```

- BindingResult는 앞서있는 modelAttribute를 target으로 삼음.
  log.info("objectName={}", bindingResult.getObjectName());
  log.info("target={}", bindingResult.getTarget());
  2024-01-10 14:33:18.414  INFO 63652 --- [nio-8080-exec-3] h.i.w.v.ValidationItemControllerV2       : objectName=item
  2024-01-10 14:33:18.415  INFO 63652 --- [nio-8080-exec-3] h.i.w.v.ValidationItemControllerV2       : target=Item(id=null, itemName=1, price=null, quantity=null)

```
- new FieldError("item", "price", item.getPrice(), false, new String[]
{"range.item.price"}, new Object[]{1000, 1000000}

// codes : required.item.itemName 를 사용해서 msg 코드를 지정
// arguments : Object[]{1000, 1000000} 를 사용해 메시지코드에 {0}, {1} 로 아규먼트 전달
```

- rejectValue(), reject() 사용하면 fieldError, ObjectError를 직접생성하지 않아도됨.
  errors.properties 코드직접입력하지 않았는데 정상출력된다 !?
```
 # 메시지코드코드는 requried.item.itemName이나 requried만 입력해도 정상출력됨.
 bindingResult.rejectValue("itemName", "required");
```
- 메시지를 높은 우선순위를 사용하기때문에
```
#errors.properties
#Level1
required.item.itemName: 상품 이름은 필수 입니다.
#Level2
required: 필수 값 입니다.
```