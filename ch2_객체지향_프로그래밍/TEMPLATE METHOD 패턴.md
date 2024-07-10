<p style="text-align:end">
	2024-07-11 04:24 | 디자인 패턴
</p>

> [!NOTE] TEMPLATE METHOD 패턴이란?
> 알고리즘의 구조(뼈대, skeleton)를 정의하고 일부 단계를 서브 클래스에서 구체적으로 구현할 수 있게 하는 행동 패턴

- **템플릿(Template)**
    - 여러 클래스에서 공통으로 사용하는 메서드
    - 상위클래스에서 정의, 서브 클래스마다 다르게 구현

# 사용 상황 예시

---

- 회사 문서를 분석하는 앱을 만드는 상황
- 다양한 포맷(doc, csv, pdf) 문서에 대해 일관된 형식으로 의미있는 데이터 추출

![[Pasted image 20240711042617.png]]

- `extractXXXData(file)`, `parseXXXData(file)`를 제외한 나머지 코드들은 중복된다.
- 알고리즘의 중복되는 부분을 템플릿으로 만들면 더 편해지지 않을까?

# 아이디어

---

1. 알고리즘을 일련의 단계들로 나누고 이러한 단계를 메서드로 변환
2. 단일 템플릿 메서드 내부에, 단계 메서드들에 대한 일련의 호출로 구성
3. 상속을 통해 추상 단계 메서드를 오버라이드 해서 구현

![[Pasted image 20240711042809.png|500]]

|         -         |                  설명                  |                  비고                   |
| :---------------: | :----------------------------------: | :-----------------------------------: |
|  **템플릿 <br>메서드**  |   알고리즘의 뼈대를 이루는 메서드.<br>단계 메서드로 구성   | 서브 클래스에서 오버라이딩 되면 안 되므로 ==final 처리==  |
| **추상 <br>단계 메서드** |       모든 자식 클래스에서 구현해야 하는 메서드        |                                       |
|  **디폴트 단계 메서드**   | 부모 클래스에서 기본 구현이 있어서 공통으로 쓰일 수 있는 메서드 | 필요한 경우 디폴트 구현을 무시하고 자식에서 오버라이드 할 수 있음 |

# Template Method Pattern 구조

---

![[Pasted image 20240711043359.png|600]]

# Example

---

- 지정된 경로(path)의 파일에서 숫자를 읽어와 숫자들을 연산한 결과를 알려주는 기능 구현
    - `sum`: 모든 숫자를 더하는 기능
	    - e.g. $(0)+1+2+3+4+5$ 
    - `multiply`: 모든 숫자를 곱하는 기능
	    - e.g. $(1)*1*2*3*4*5$

- 이전 값에 더하냐(`sum`), 곱하냐(`multiply`)의 차이만 있고, 뼈대(skeleton)은 동일
    - 초기값은 차이가 존재 (더하기: 0, 곱하기: 1)

![[Pasted image 20240711043451.png]]

+ **템플릿 메서드**: `process()`
+ **추상 단계 메서드**:
	- `calculate(int, int)` : 더하기/곱하기 연산
	- `getInitial()` : 더하기/곱하기 연산의 초기값

## AbstractClass

```java
abstract class FileProcessor {
	private String path;

	public FileProcessor(String path){ this.path = path; }

	// 템플릿 메서드
	// 서브 클래스에서 오버라이딩 되면 안되므로 final 처리
	public final int process(){
		try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
			int result = getInitial();
			String line = null;

			while ((line = reader.readLine()) != null) {
				result = calculate(result, Integer.parseInt(line));
			}
			return result;
		} catch (IOException e) {
			throw new IllegalArgumentException(path + "is not found", e);
		}
	}

	// 추상 단계 메서드
	protected abstract int calculate(int result, int number);
	protected abstract int getInitial();
}
```

## ConcreteClass

```java
public class SumFileProcessor extends FileProcessor {
	public SumFileProcessor(String path) {super(path);}

	@Override
	protected int calculate(int result, int number) {return result + number;}

	@Override
	protected int getInitial() {return 0;}
}

public class MultiplyFileProcessor extends FileProcessor {
	public MultiplyFileProcessor(String path) {super(path);}

	@Override
	protected int calculate(int result, int number) {return result * number;}
	@Override
	protected int getInitial() {return 1;}
}
```

# 훅(hook) 메서드

---

- ==몸체가 비어 있는 단계 메서드==
- 자식 클래스가 선택적으로 오버라이딩 할 수 있음
    - 템플릿 메서드는 훅이 오버라이딩이 되지 않아도 작동
- 훅 메서드는 알고리즘의 전/후에 배치되어 자식 클래스들에게 알고리즘의 **추가 확장 지점**을 제공

```java
abstract class AbstractClass {
	// 템플릿 메서드
	public final void templateMethod() {
		Operation1();
		hook(); // hook 메서드 호출
		Operation2();
	}

	// hook 메서드
	// 추상 메서드가 아니기 때문에, 자식 클래스에서 반드시 오버라이딩하지 않아도 됨
	protected void hook() {
		// 기본 동작 또는 빈 동작
	}

	protected abstract void Operation1();
	protected abstract void Operation2();
}
```

## Example - w/hook

커피를 제조하는 템플릿(`makeCoffee`)을 구현한다.

- 자식 클래스는 `customerWantsCondiments` 훅 메서드를 선택적으로 오버라이딩 함
- 템플릿 메서드(`makeCoffee`)는 훅 메서드의 결과에 따라 `addCondiments` 메서드를 호출할지에 대한 추가 확장을 결정

### AbstractClass

```java
abstract class CoffeeTemplate {

	// 템플릿 메서드
	public final void makeCoffee() {
		boilWater();
		brewCoffeeGrounds();
		pourInCup();

		// hook 메서드 호출
		if (customerWantsCondiments()) {
			addCondiments();
		}
	}

	// hook 메서드 (기본 구현이 있는 경우)
	protected boolean customerWantsCondiments() {
		return true; // 기본적으로는 조미료 추가를 원함
	}

	protected abstract void boilWater();
	protected abstract void brewCoffeeGrounds();
	protected abstract void pourInCup();
	protected abstract void addCondiments();
}
```

### ConcreteClass

```java
class CoffeeWithHook extends CoffeeTemplate {

	// hook 메서드 오버라이드
	protected boolean customerWantsCondiments() {
		// 사용자가 조미료를 원하는지 물어봄
		// 실제로는 사용자 입력 등의 방식으로 판단 가능
		return true;
	}

	protected void boilWater() {...}
	protected void brewCoffeeGrounds() {...}
	protected void pourInCup() {...}
	protected void addCondiments() {...}
}
```

# Template Method Pattern 정리

---

## 장점

- 클라이언트가 대규모 알고리즘의 특정 부분만 재정의 하도록 하여 알고리즘의 다른 부분에 발생하는 변경에 영향을 덜 받도록 함
- 상위 클래스로 로직을 공통화 하여 코드의 중복을 줄일 수 있고, 핵심 로직을 상위 클래스에 관리 하므로 관리가 용이해짐

## 단점

- 제공되는 뼈대로 인해 알고리즘 로직의 유연성이 제한 될 수 있음
- 하위 클래스에서 구현할 때, 해당 단계 메소드가 어느 타이밍에 호출되는지 템플릿 메서드 로직을 이해할 필요가 있음
- 로직에 변화가 생겨 상위 클래스가 수정되면 모든 서브 클래스 수정이 생김
- 서브 클래스를 통해 기본 단계 구현을 억제하여 리스코프 치환 법칙을 위반할 여지가 있음
    - e.g. 디폴트 단계 메서드를 서브 클래스에서 오버라이드했을 때, 아래 두 `fileProcessor`는 다른 동작을 수행한다.
        - `FileProcessor fileProcessor = new SumFileProcessor()`;
        - `SumFileProcessor fileProcessor = new SumFileProcessor();`