<template>
  <div class="pattern-container">
    <h1>Visitor Pattern</h1>
    <p class="description">
      방문자 패턴은 객체 구조와 동작을 분리하는 행동 패턴입니다.
      데이터 형태와 처리 알고리즘을 독립적으로 관리하며, 기존 클래스 수정 없이 새로운 기능을 외부에서 확장할 수 있습니다.
    </p>

    <div class="demo-section">
      <h2>A. File System Visitor (파일 시스템)</h2>
      <p>파일과 폴더에 대해 크기 계산, 검색 등 다양한 연산을 수행합니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Visitor 인터페이스
interface FileVisitor {
  visitFile(file: FileElement): void
  visitFolder(folder: FolderElement): void
}

// Element 인터페이스
interface FileSystemElement {
  accept(visitor: FileVisitor): void
}

// ConcreteElement - 파일
class FileElement implements FileSystemElement {
  constructor(public name: string, public size: number) {}

  accept(visitor: FileVisitor): void {
    visitor.visitFile(this)
  }
}

// ConcreteElement - 폴더
class FolderElement implements FileSystemElement {
  public children: FileSystemElement[] = []

  constructor(public name: string) {}

  add(element: FileSystemElement): void {
    this.children.push(element)
  }

  accept(visitor: FileVisitor): void {
    visitor.visitFolder(this)
    // 자식 요소들도 방문
    for (const child of this.children) {
      child.accept(visitor)
    }
  }
}

// ConcreteVisitor - 크기 계산기
class SizeCalculator implements FileVisitor {
  public totalSize: number = 0

  visitFile(file: FileElement): void {
    this.totalSize += file.size
  }

  visitFolder(folder: FolderElement): void {
    // 폴더 자체는 크기가 없음
  }
}

// ConcreteVisitor - 파일 검색기
class FileSearcher implements FileVisitor {
  public foundFiles: string[] = []

  constructor(private searchTerm: string) {}

  visitFile(file: FileElement): void {
    if (file.name.includes(this.searchTerm)) {
      this.foundFiles.push(file.name)
    }
  }

  visitFolder(folder: FolderElement): void {
    // 폴더 이름은 검색하지 않음
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <div class="form-group">
          <label for="file-search-term">파일 검색어:</label>
          <input
            id="file-search-term"
            v-model="fileSearchTerm"
            type="text"
            class="input-field"
            placeholder="예: .jpg, photo, report"
          />
        </div>
        <button @click="runFileSystemVisitors" class="test-btn">
          크기 계산 및 파일 검색 실행
        </button>
      </div>

      <div v-if="fileSystemResult" class="result">
        <h3>실행 결과</h3>
        <pre>{{ fileSystemResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>B. Shape Visitor (도형 계산)</h2>
      <p>다양한 도형에 대해 면적 계산, 그리기, 내보내기 등의 연산을 수행합니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Visitor 인터페이스
interface ShapeVisitor {
  visitCircle(circle: Circle): void
  visitRectangle(rectangle: Rectangle): void
  visitTriangle(triangle: Triangle): void
}

// Element 인터페이스
interface Shape {
  accept(visitor: ShapeVisitor): void
}

// ConcreteElements
class Circle implements Shape {
  constructor(public radius: number) {}

  accept(visitor: ShapeVisitor): void {
    visitor.visitCircle(this)
  }
}

class Rectangle implements Shape {
  constructor(public width: number, public height: number) {}

  accept(visitor: ShapeVisitor): void {
    visitor.visitRectangle(this)
  }
}

class Triangle implements Shape {
  constructor(public base: number, public height: number) {}

  accept(visitor: ShapeVisitor): void {
    visitor.visitTriangle(this)
  }
}

// ConcreteVisitor - 면적 계산기
class AreaCalculator implements ShapeVisitor {
  public totalArea: number = 0

  visitCircle(circle: Circle): void {
    this.totalArea += Math.PI * circle.radius ** 2
  }

  visitRectangle(rectangle: Rectangle): void {
    this.totalArea += rectangle.width * rectangle.height
  }

  visitTriangle(triangle: Triangle): void {
    this.totalArea += (triangle.base * triangle.height) / 2
  }
}

// ConcreteVisitor - 둘레 계산기
class PerimeterCalculator implements ShapeVisitor {
  public totalPerimeter: number = 0

  visitCircle(circle: Circle): void {
    this.totalPerimeter += 2 * Math.PI * circle.radius
  }

  visitRectangle(rectangle: Rectangle): void {
    this.totalPerimeter += 2 * (rectangle.width + rectangle.height)
  }

  visitTriangle(triangle: Triangle): void {
    // 직각삼각형 가정
    const hypotenuse = Math.sqrt(
      triangle.base ** 2 + triangle.height ** 2
    )
    this.totalPerimeter += triangle.base + triangle.height + hypotenuse
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h4>도형 속성 입력</h4>
        <div class="form-group">
          <label>원 (반지름):</label>
          <input v-model.number="circleRadius" type="number" class="input-field" />
        </div>
        <div class="form-group">
          <label>사각형 (너비, 높이):</label>
          <div style="display: flex; gap: 10px">
            <input
              v-model.number="rectWidth"
              type="number"
              class="input-field"
              placeholder="너비"
            />
            <input
              v-model.number="rectHeight"
              type="number"
              class="input-field"
              placeholder="높이"
            />
          </div>
        </div>
        <div class="form-group">
          <label>삼각형 (밑변, 높이):</label>
          <div style="display: flex; gap: 10px">
            <input
              v-model.number="triBase"
              type="number"
              class="input-field"
              placeholder="밑변"
            />
            <input
              v-model.number="triHeight"
              type="number"
              class="input-field"
              placeholder="높이"
            />
          </div>
        </div>
        <button @click="testShapeVisitor" class="test-btn">면적 및 둘레 계산</button>
      </div>

      <div v-if="shapeResult" class="result">
        <h3>실행 결과</h3>
        <pre>{{ shapeResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>C. Shopping Cart Visitor (장바구니)</h2>
      <p>장바구니의 상품들에 대해 가격 계산, 할인 적용, 세금 계산을 수행합니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Visitor 인터페이스
interface ItemVisitor {
  visitBook(book: Book): void
  visitElectronics(electronics: Electronics): void
  visitFood(food: Food): void
}

// Element 인터페이스
interface Item {
  accept(visitor: ItemVisitor): void
}

// ConcreteElements
class Book implements Item {
  constructor(public title: string, public price: number) {}

  accept(visitor: ItemVisitor): void {
    visitor.visitBook(this)
  }
}

class Electronics implements Item {
  constructor(public name: string, public price: number) {}

  accept(visitor: ItemVisitor): void {
    visitor.visitElectronics(this)
  }
}

class Food implements Item {
  constructor(public name: string, public price: number) {}

  accept(visitor: ItemVisitor): void {
    visitor.visitFood(this)
  }
}

// ConcreteVisitor - 가격 계산기 (할인 적용)
class PriceCalculator implements ItemVisitor {
  public total: number = 0

  visitBook(book: Book): void {
    // 책은 10% 할인
    this.total += book.price * 0.9
  }

  visitElectronics(electronics: Electronics): void {
    // 전자제품은 할인 없음
    this.total += electronics.price
  }

  visitFood(food: Food): void {
    // 식품은 5% 할인
    this.total += food.price * 0.95
  }
}

// ConcreteVisitor - 세금 계산기
class TaxCalculator implements ItemVisitor {
  public totalTax: number = 0

  visitBook(book: Book): void {
    // 책은 면세
    this.totalTax += 0
  }

  visitElectronics(electronics: Electronics): void {
    // 전자제품은 10% 세금
    this.totalTax += electronics.price * 0.1
  }

  visitFood(food: Food): void {
    // 식품은 5% 세금
    this.totalTax += food.price * 0.05
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h4>장바구니 상품 가격 입력</h4>
        <div class="form-group">
          <label>책 가격:</label>
          <input v-model.number="bookPrice" type="number" class="input-field" />
        </div>
        <div class="form-group">
          <label>전자제품 가격:</label>
          <input v-model.number="electronicsPrice" type="number" class="input-field" />
        </div>
        <div class="form-group">
          <label>식품 가격:</label>
          <input v-model.number="foodPrice" type="number" class="input-field" />
        </div>
        <button @click="testShoppingCartVisitor" class="test-btn">할인 및 세금 계산</button>
      </div>

      <div v-if="cartResult" class="result">
        <h3>실행 결과</h3>
        <pre>{{ cartResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>D. Employee Visitor (직원 급여 계산)</h2>
      <p>다양한 직급의 직원에 대해 급여, 보너스, 연차를 계산합니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Visitor 인터페이스
interface EmployeeVisitor {
  visitManager(manager: Manager): void
  visitDeveloper(developer: Developer): void
  visitIntern(intern: Intern): void
}

// Element 인터페이스
interface Employee {
  accept(visitor: EmployeeVisitor): void
}

// ConcreteElements
class Manager implements Employee {
  constructor(public name: string, public baseSalary: number) {}

  accept(visitor: EmployeeVisitor): void {
    visitor.visitManager(this)
  }
}

class Developer implements Employee {
  constructor(public name: string, public baseSalary: number) {}

  accept(visitor: EmployeeVisitor): void {
    visitor.visitDeveloper(this)
  }
}

class Intern implements Employee {
  constructor(public name: string, public baseSalary: number) {}

  accept(visitor: EmployeeVisitor): void {
    visitor.visitIntern(this)
  }
}

// ConcreteVisitor - 급여 계산기
class SalaryCalculator implements EmployeeVisitor {
  public totalSalary: number = 0

  visitManager(manager: Manager): void {
    // 매니저는 기본급 + 50% 보너스
    this.totalSalary += manager.baseSalary * 1.5
  }

  visitDeveloper(developer: Developer): void {
    // 개발자는 기본급 + 20% 보너스
    this.totalSalary += developer.baseSalary * 1.2
  }

  visitIntern(intern: Intern): void {
    // 인턴은 기본급만
    this.totalSalary += intern.baseSalary
  }
}

// ConcreteVisitor - 연차 계산기
class VacationCalculator implements EmployeeVisitor {
  public totalDays: number = 0

  visitManager(manager: Manager): void {
    this.totalDays += 20 // 매니저는 20일
  }

  visitDeveloper(developer: Developer): void {
    this.totalDays += 15 // 개발자는 15일
  }

  visitIntern(intern: Intern): void {
    this.totalDays += 10 // 인턴은 10일
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h4>직원 급여 입력</h4>
        <div class="form-group">
          <label>매니저 기본급:</label>
          <input v-model.number="managerSalary" type="number" class="input-field" />
        </div>
        <div class="form-group">
          <label>개발자 기본급:</label>
          <input v-model.number="developerSalary" type="number" class="input-field" />
        </div>
        <div class="form-group">
          <label>인턴 기본급:</label>
          <input v-model.number="internSalary" type="number" class="input-field" />
        </div>
        <button @click="testEmployeeVisitor" class="test-btn">급여 및 연차 계산</button>
      </div>

      <div v-if="employeeResult" class="result">
        <h3>실행 결과</h3>
        <pre>{{ employeeResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>패턴의 장점</h2>
      <div class="benefits-list">
        <h3>주요 장점</h3>
        <ul>
          <li><strong>OCP 준수:</strong> 새 연산 추가 시 기존 클래스 수정 불필요</li>
          <li><strong>SRP 준수:</strong> 각 Visitor가 단일 연산만 담당</li>
          <li><strong>관련 동작 집중:</strong> 비슷한 연산들을 한 곳에 모음</li>
          <li><strong>이중 디스패치:</strong> 런타임에 정확한 메서드 호출</li>
          <li><strong>쉬운 확장:</strong> 새로운 Visitor만 추가하면 됨</li>
        </ul>
      </div>

      <div class="benefits-list">
        <h3>단점</h3>
        <ul>
          <li><strong>Element 추가 어려움:</strong> 새 Element 타입 추가 시 모든 Visitor 수정 필요</li>
          <li><strong>캡슐화 위반:</strong> Visitor가 Element의 내부 상태에 접근</li>
        </ul>
      </div>

      <div class="code-block">
        <h3>실무 활용 사례</h3>
        <pre><code>// 1. 컴파일러 AST(추상 구문 트리) 처리
class TypeChecker implements ASTVisitor {
  visitFunctionNode(node: FunctionNode): void { }
  visitVariableNode(node: VariableNode): void { }
}

// 2. DOM 탐색 및 조작
class DOMTraverser implements NodeVisitor {
  visitElement(element: Element): void { }
  visitTextNode(text: Text): void { }
}

// 3. JSON/XML 변환
class XMLExporter implements DataVisitor {
  visitObject(obj: ObjectNode): string { }
  visitArray(arr: ArrayNode): string { }
}

// 4. 보고서 생성
class ReportGenerator implements EntityVisitor {
  visitCustomer(customer: Customer): void { }
  visitOrder(order: Order): void { }
}

// 5. 게임 엔티티 처리
class RenderVisitor implements EntityVisitor {
  visitPlayer(player: Player): void { }
  visitEnemy(enemy: Enemy): void { }
}</code></pre>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const fileSystemResult = ref<string>('')
const shapeResult = ref<string>('')
const cartResult = ref<string>('')
const employeeResult = ref<string>('')

const fileSearchTerm = ref<string>('.jpg')

const circleRadius = ref<number>(5)
const rectWidth = ref<number>(10)
const rectHeight = ref<number>(20)
const triBase = ref<number>(8)
const triHeight = ref<number>(6)

const bookPrice = ref<number>(30000)
const electronicsPrice = ref<number>(1500000)
const foodPrice = ref<number>(5000)

const managerSalary = ref<number>(5000000)
const developerSalary = ref<number>(4000000)
const internSalary = ref<number>(2000000)

// ============ A. File System Visitor ============

interface FileVisitor {
  visitFile(file: FileElement): void
  visitFolder(folder: FolderElement): void
}

interface FileSystemElement {
  accept(visitor: FileVisitor): void
}

class FileElement implements FileSystemElement {
  constructor(public name: string, public size: number) {}

  accept(visitor: FileVisitor): void {
    visitor.visitFile(this)
  }
}

class FolderElement implements FileSystemElement {
  public children: FileSystemElement[] = []

  constructor(public name: string) {}

  add(element: FileSystemElement): void {
    this.children.push(element)
  }

  accept(visitor: FileVisitor): void {
    visitor.visitFolder(this)
    for (const child of this.children) {
      child.accept(visitor)
    }
  }
}

class SizeCalculator implements FileVisitor {
  public totalSize: number = 0

  visitFile(file: FileElement): void {
    this.totalSize += file.size
  }

  visitFolder(folder: FolderElement): void {
    // 폴더 자체는 크기가 없음
  }
}

class FileSearcher implements FileVisitor {
  public foundFiles: string[] = []

  constructor(private searchTerm: string) {}

  visitFile(file: FileElement): void {
    if (file.name.includes(this.searchTerm)) {
      this.foundFiles.push(file.name)
    }
  }

  visitFolder(folder: FolderElement): void {
    // 폴더 이름은 검색하지 않음
  }
}

function runFileSystemVisitors() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = fileSystemResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  // 파일 시스템 구조 생성
  const root = new FolderElement('root')
  const documents = new FolderElement('documents')
  const pictures = new FolderElement('pictures')

  documents.add(new FileElement('report.pdf', 1500))
  documents.add(new FileElement('presentation.pptx', 3000))
  pictures.add(new FileElement('photo1.jpg', 2000))
  pictures.add(new FileElement('photo2.jpg', 2500))

  root.add(documents)
  root.add(pictures)
  root.add(new FileElement('readme.txt', 500))

  const logs: string[] = []

  // Visitor 1: 전체 크기 계산
  const sizeCalc = new SizeCalculator()
  root.accept(sizeCalc)
  logs.push(`[크기 계산 Visitor]`)
  logs.push(`- 전체 파일 크기: ${sizeCalc.totalSize}KB`)

  // Visitor 2: 파일 검색
  const searcher = new FileSearcher(fileSearchTerm.value)
  root.accept(searcher)
  logs.push(`\n[파일 검색 Visitor (검색어: "${fileSearchTerm.value}")]`)
  if (searcher.foundFiles.length > 0) {
    logs.push(`- 찾은 파일: ${searcher.foundFiles.join(', ')}`)
  } else {
    logs.push(`- "${fileSearchTerm.value}" 포함된 파일 없음`)
  }

  fileSystemResult.value =
    separator +
    `[${timestamp}]
${logs.join('\n')}

✅ 두 개의 다른 Visitor가 동일한 파일 구조를 방문했습니다.
💡 Visitor 패턴: 객체 구조(파일 시스템)를 변경하지 않고 새로운 연산(크기 계산, 검색)을 추가할 수 있습니다.`
}

// ============ B. Shape Visitor ============

interface ShapeVisitor {
  visitCircle(circle: Circle): void
  visitRectangle(rectangle: Rectangle): void
  visitTriangle(triangle: Triangle): void
}

interface Shape {
  accept(visitor: ShapeVisitor): void
}

class Circle implements Shape {
  constructor(public radius: number) {}

  accept(visitor: ShapeVisitor): void {
    visitor.visitCircle(this)
  }
}

class Rectangle implements Shape {
  constructor(public width: number, public height: number) {}

  accept(visitor: ShapeVisitor): void {
    visitor.visitRectangle(this)
  }
}

class Triangle implements Shape {
  constructor(public base: number, public height: number) {}

  accept(visitor: ShapeVisitor): void {
    visitor.visitTriangle(this)
  }
}

class AreaCalculator implements ShapeVisitor {
  public totalArea: number = 0

  visitCircle(circle: Circle): void {
    this.totalArea += Math.PI * circle.radius ** 2
  }

  visitRectangle(rectangle: Rectangle): void {
    this.totalArea += rectangle.width * rectangle.height
  }

  visitTriangle(triangle: Triangle): void {
    this.totalArea += (triangle.base * triangle.height) / 2
  }
}

class PerimeterCalculator implements ShapeVisitor {
  public totalPerimeter: number = 0

  visitCircle(circle: Circle): void {
    this.totalPerimeter += 2 * Math.PI * circle.radius
  }

  visitRectangle(rectangle: Rectangle): void {
    this.totalPerimeter += 2 * (rectangle.width + rectangle.height)
  }

  visitTriangle(triangle: Triangle): void {
    const hypotenuse = Math.sqrt(triangle.base ** 2 + triangle.height ** 2)
    this.totalPerimeter += triangle.base + triangle.height + hypotenuse
  }
}

function testShapeVisitor() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = shapeResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const shapes: Shape[] = [new Circle(5), new Rectangle(10, 20), new Triangle(8, 6)]

  const logs: string[] = []
  logs.push('도형 목록: 원(r=5), 사각형(10x20), 삼각형(8x6)')

  // Visitor 1: 면적 계산
  const areaCalc = new AreaCalculator()
  for (const shape of shapes) {
    shape.accept(areaCalc)
  }
  logs.push(`\n[면적 계산 Visitor]`)
  logs.push(`- 전체 면적: ${areaCalc.totalArea.toFixed(2)}`)

  // Visitor 2: 둘레 계산
  const perimCalc = new PerimeterCalculator()
  for (const shape of shapes) {
    shape.accept(perimCalc)
  }
  logs.push(`\n[둘레 계산 Visitor]`)
  logs.push(`- 전체 둘레: ${perimCalc.totalPerimeter.toFixed(2)}`)

  shapeResult.value =
    separator +
    `[${timestamp}]
${logs.join('\n')}

✅ 도형 클래스 수정 없이 두 가지 다른 계산(면적, 둘레)을 수행했습니다.
💡 Visitor 패턴: 데이터 구조(도형)와 알고리즘(계산)을 분리합니다.`
}

// ============ C. Shopping Cart Visitor ============

interface ItemVisitor {
  visitBook(book: Book): void
  visitElectronics(electronics: Electronics): void
  visitFood(food: Food): void
}

interface Item {
  accept(visitor: ItemVisitor): void
}

class Book implements Item {
  constructor(public title: string, public price: number) {}

  accept(visitor: ItemVisitor): void {
    visitor.visitBook(this)
  }
}

class Electronics implements Item {
  constructor(public name: string, public price: number) {}

  accept(visitor: ItemVisitor): void {
    visitor.visitElectronics(this)
  }
}

class Food implements Item {
  constructor(public name: string, public price: number) {}

  accept(visitor: ItemVisitor): void {
    visitor.visitFood(this)
  }
}

class PriceCalculator implements ItemVisitor {
  public total: number = 0

  visitBook(book: Book): void {
    this.total += book.price * 0.9 // 10% 할인
  }

  visitElectronics(electronics: Electronics): void {
    this.total += electronics.price // 할인 없음
  }

  visitFood(food: Food): void {
    this.total += food.price * 0.95 // 5% 할인
  }
}

class TaxCalculator implements ItemVisitor {
  public totalTax: number = 0

  visitBook(book: Book): void {
    this.totalTax += 0 // 면세
  }

  visitElectronics(electronics: Electronics): void {
    this.totalTax += electronics.price * 0.1 // 10% 세금
  }

  visitFood(food: Food): void {
    this.totalTax += food.price * 0.05 // 5% 세금
  }
}

function testShoppingCartVisitor() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = cartResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const cart: Item[] = [
    new Book('디자인 패턴', bookPrice.value),
    new Electronics('노트북', electronicsPrice.value),
    new Food('사과', foodPrice.value)
  ]

  const logs: string[] = []
  logs.push(
    `장바구니: 책(${bookPrice.value.toLocaleString()}원), 노트북(${electronicsPrice.value.toLocaleString()}원), 사과(${foodPrice.value.toLocaleString()}원)`
  )

  // Visitor 1: 할인 가격 계산
  const priceCalc = new PriceCalculator()
  for (const item of cart) {
    item.accept(priceCalc)
  }
  logs.push(`\n[할인 적용 Visitor]`)
  logs.push(`- 책(10%), 식품(5%) 할인 적용`)
  logs.push(`- 할인 후 금액: ${priceCalc.total.toLocaleString()}원`)

  // Visitor 2: 세금 계산
  const taxCalc = new TaxCalculator()
  for (const item of cart) {
    item.accept(taxCalc)
  }
  logs.push(`\n[세금 계산 Visitor]`)
  logs.push(`- 책(면세), 전자제품(10%), 식품(5%) 세금 적용`)
  logs.push(`- 총 세금: ${taxCalc.totalTax.toLocaleString()}원`)

  logs.push(`\n=> 최종 결제 금액: ${(priceCalc.total + taxCalc.totalTax).toLocaleString()}원`)

  cartResult.value =
    separator +
    `[${timestamp}]
${logs.join('\n')}

✅ 상품 클래스 수정 없이 할인과 세금 계산 로직을 각각 적용했습니다.
💡 Visitor 패턴: 복잡한 계산 규칙을 객체 구조에서 분리하여 관리합니다.`
}

// ============ D. Employee Visitor ============

interface EmployeeVisitor {
  visitManager(manager: Manager): void
  visitDeveloper(developer: Developer): void
  visitIntern(intern: Intern): void
}

interface Employee {
  accept(visitor: EmployeeVisitor): void
}

class Manager implements Employee {
  constructor(public name: string, public baseSalary: number) {}

  accept(visitor: EmployeeVisitor): void {
    visitor.visitManager(this)
  }
}

class Developer implements Employee {
  constructor(public name: string, public baseSalary: number) {}

  accept(visitor: EmployeeVisitor): void {
    visitor.visitDeveloper(this)
  }
}

class Intern implements Employee {
  constructor(public name: string, public baseSalary: number) {}

  accept(visitor: EmployeeVisitor): void {
    visitor.visitIntern(this)
  }
}

class SalaryCalculator implements EmployeeVisitor {
  public totalSalary: number = 0

  visitManager(manager: Manager): void {
    this.totalSalary += manager.baseSalary * 1.5 // 50% 보너스
  }

  visitDeveloper(developer: Developer): void {
    this.totalSalary += developer.baseSalary * 1.2 // 20% 보너스
  }

  visitIntern(intern: Intern): void {
    this.totalSalary += intern.baseSalary // 보너스 없음
  }
}

class VacationCalculator implements EmployeeVisitor {
  public totalDays: number = 0

  visitManager(manager: Manager): void {
    this.totalDays += 20
  }

  visitDeveloper(developer: Developer): void {
    this.totalDays += 15
  }

  visitIntern(intern: Intern): void {
    this.totalDays += 10
  }
}

function testEmployeeVisitor() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = employeeResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const employees: Employee[] = [
    new Manager('김매니저', managerSalary.value),
    new Developer('이개발', developerSalary.value),
    new Developer('박개발', developerSalary.value),
    new Intern('최인턴', internSalary.value)
  ]

  const logs: string[] = []
  logs.push(
    `직원: 매니저(${managerSalary.value.toLocaleString()}원), 개발자(${developerSalary.value.toLocaleString()}원), 개발자(${developerSalary.value.toLocaleString()}원), 인턴(${internSalary.value.toLocaleString()}원)`
  )

  // Visitor 1: 급여 계산
  const salaryCalc = new SalaryCalculator()
  for (const employee of employees) {
    employee.accept(salaryCalc)
  }
  logs.push(`\n[급여 계산 Visitor]`)
  logs.push(`- 매니저(x1.5), 개발자(x1.2) 보너스 적용`)
  logs.push(`- 총 급여: ${salaryCalc.totalSalary.toLocaleString()}원`)

  // Visitor 2: 연차 계산
  const vacationCalc = new VacationCalculator()
  for (const employee of employees) {
    employee.accept(vacationCalc)
  }
  logs.push(`\n[연차 계산 Visitor]`)
  logs.push(`- 매니저(20일), 개발자(15일), 인턴(10일)`)
  logs.push(`- 총 연차: ${vacationCalc.totalDays}일`)

  employeeResult.value =
    separator +
    `[${timestamp}]
${logs.join('\n')}

✅ 직원 클래스 수정 없이 급여와 연차 계산 로직을 분리하여 실행했습니다.
💡 Visitor 패턴: 다양한 객체 타입에 대한 연산을 중앙에서 관리합니다.`
}
</script>

<style scoped>
/* VisitorPattern 전용 스타일이 필요한 경우 여기에 추가 */
/* 공통 스타일은 src/assets/main.css에서 전역으로 적용됨 */
</style>
