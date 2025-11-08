<template>
  <div class="pattern-container">
    <h1>Decorator Pattern</h1>
    <p class="description">
      데코레이터 패턴은 객체에 새로운 기능을 동적으로 추가하는 구조 패턴입니다.
      상속 대신 기존 객체를 감싸는 방식으로 기능을 확장하며, 필요한 기능만 블록처럼 조합할 수 있습니다.
    </p>

    <div class="demo-section">
      <h2>A. Notifier Decorator (알림 시스템)</h2>
      <p>기본 알림에 이메일, SMS, Slack 등의 기능을 동적으로 추가합니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Component 인터페이스
interface Notifier {
  send(message: string): string
}

// ConcreteComponent - 기본 구현
class BaseNotifier implements Notifier {
  send(message: string): string {
    return `[기본 알림] ${message}`
  }
}

// Decorator 추상 클래스
abstract class NotifierDecorator implements Notifier {
  constructor(protected wrappee: Notifier) {}

  send(message: string): string {
    return this.wrappee.send(message)
  }
}

// ConcreteDecorator - 이메일 기능 추가
class EmailNotifier extends NotifierDecorator {
  send(message: string): string {
    return super.send(message) + '\n[이메일 발송] ' + message
  }
}

// ConcreteDecorator - SMS 기능 추가
class SmsNotifier extends NotifierDecorator {
  send(message: string): string {
    return super.send(message) + '\n[SMS 발송] ' + message
  }
}

// ConcreteDecorator - Slack 기능 추가
class SlackNotifier extends NotifierDecorator {
  send(message: string): string {
    return super.send(message) + '\n[Slack 발송] ' + message
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>📢 알림 채널 선택 및 메시지 전송</h3>

        <div class="form-group">
          <label for="notifier-message">메시지:</label>
          <input
            id="notifier-message"
            v-model="notifierMessage"
            type="text"
            placeholder="예: 긴급 알림: 서버 점검 예정"
            class="input-field"
          />
        </div>

        <div class="decorator-selection">
          <label>알림 채널 선택 (중복 가능):</label>
          <button
            @click="toggleNotifier('email')"
            :class="['decorator-btn', { active: selectedNotifiers.includes('email') }]"
          >
            📧 이메일
          </button>
          <button
            @click="toggleNotifier('sms')"
            :class="['decorator-btn', { active: selectedNotifiers.includes('sms') }]"
          >
            📱 SMS
          </button>
          <button
            @click="toggleNotifier('slack')"
            :class="['decorator-btn', { active: selectedNotifiers.includes('slack') }]"
          >
            💬 Slack
          </button>
        </div>

        <button @click="sendNotification" class="test-btn" :disabled="!notifierMessage">
          알림 전송 (기본 + 선택된 채널)
        </button>
      </div>

      <div v-if="notifierResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ notifierResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>B. Coffee Decorator (커피 주문 시스템)</h2>
      <p>기본 커피에 우유, 시럽, 휘핑크림 등의 토핑을 추가하여 가격을 계산합니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Component 인터페이스
interface Coffee {
  getDescription(): string
  getCost(): number
}

// ConcreteComponent
class SimpleCoffee implements Coffee {
  getDescription(): string {
    return '기본 커피'
  }
  getCost(): number {
    return 2000
  }
}

// Decorator
abstract class CoffeeDecorator implements Coffee {
  constructor(protected coffee: Coffee) {}

  getDescription(): string {
    return this.coffee.getDescription()
  }

  getCost(): number {
    return this.coffee.getCost()
  }
}

// ConcreteDecorators
class MilkDecorator extends CoffeeDecorator {
  getDescription(): string {
    return this.coffee.getDescription() + ', 우유'
  }
  getCost(): number {
    return this.coffee.getCost() + 500
  }
}

class CaramelDecorator extends CoffeeDecorator {
  getDescription(): string {
    return this.coffee.getDescription() + ', 카라멜 시럽'
  }
  getCost(): number {
    return this.coffee.getCost() + 700
  }
}

class WhippedCreamDecorator extends CoffeeDecorator {
  getDescription(): string {
    return this.coffee.getDescription() + ', 휘핑크림'
  }
  getCost(): number {
    return this.coffee.getCost() + 800
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>☕ 커피 토핑 선택 및 주문</h3>

        <div class="decorator-selection">
          <label>토핑 선택 (중복 가능):</label>
          <button
            @click="toggleTopping('milk')"
            :class="['decorator-btn', { active: selectedToppings.includes('milk') }]"
          >
            🥛 우유 (+500원)
          </button>
          <button
            @click="toggleTopping('caramel')"
            :class="['decorator-btn', { active: selectedToppings.includes('caramel') }]"
          >
            🍯 카라멜 시럽 (+700원)
          </button>
          <button
            @click="toggleTopping('whipped')"
            :class="['decorator-btn', { active: selectedToppings.includes('whipped') }]"
          >
            🍦 휘핑크림 (+800원)
          </button>
        </div>

        <button @click="orderCoffee" class="test-btn">
          커피 주문 (기본 커피 + 선택된 토핑)
        </button>
      </div>

      <div v-if="coffeeResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ coffeeResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>C. Data Stream Decorator (파일 처리)</h2>
      <p>기본 파일 스트림에 압축, 암호화 기능을 동적으로 추가합니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Component
interface DataStream {
  writeData(data: string): string
  readData(): string
}

// ConcreteComponent
class FileStream implements DataStream {
  private data: string = ''

  writeData(data: string): string {
    this.data = data
    return `파일에 저장: ${data}`
  }

  readData(): string {
    return this.data
  }
}

// Decorator
abstract class StreamDecorator implements DataStream {
  constructor(protected stream: DataStream) {}

  writeData(data: string): string {
    return this.stream.writeData(data)
  }

  readData(): string {
    return this.stream.readData()
  }
}

// ConcreteDecorators
class CompressionDecorator extends StreamDecorator {
  writeData(data: string): string {
    const compressed = `압축됨[${data}]`
    return `[압축 적용] ${this.stream.writeData(compressed)}`
  }

  readData(): string {
    const data = this.stream.readData()
    return data.replace('압축됨[', '').replace(']', '')
  }
}

class EncryptionDecorator extends StreamDecorator {
  writeData(data: string): string {
    const encrypted = btoa(data) // Base64 인코딩
    return `[암호화 적용] ${this.stream.writeData(encrypted)}`
  }

  readData(): string {
    const data = this.stream.readData()
    try {
      return atob(data) // Base64 디코딩
    } catch {
      return data
    }
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>💾 파일 데이터 쓰기/읽기</h3>

        <div class="form-group">
          <label for="stream-data">데이터:</label>
          <input
            id="stream-data"
            v-model="streamData"
            type="text"
            placeholder="예: Sensitive User Data"
            class="input-field"
          />
        </div>

        <div class="decorator-selection">
          <label>처리 방식 선택 (중복 가능):</label>
          <button
            @click="toggleStream('compression')"
            :class="['decorator-btn', { active: selectedStreams.includes('compression') }]"
          >
            🗜️ 압축
          </button>
          <button
            @click="toggleStream('encryption')"
            :class="['decorator-btn', { active: selectedStreams.includes('encryption') }]"
          >
            🔐 암호화
          </button>
        </div>

        <button @click="processStream" class="test-btn" :disabled="!streamData">
          데이터 쓰기 및 읽기
        </button>
      </div>

      <div v-if="streamResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ streamResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>D. UI Component Decorator (React HOC 스타일)</h2>
      <p>기본 UI 컴포넌트에 로딩, 에러 처리, 로깅 기능을 추가합니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Component
interface UIComponent {
  render(): string
}

// ConcreteComponent
class Button implements UIComponent {
  constructor(private label: string) {}

  render(): string {
    return `&lt;button&gt;${this.label}&lt;/button&gt;`
  }
}

// Decorators
class LoadingDecorator implements UIComponent {
  constructor(private component: UIComponent, private isLoading: boolean) {}

  render(): string {
    if (this.isLoading) {
      return '&lt;div&gt;로딩 중...&lt;/div&gt;'
    }
    return this.component.render()
  }
}

class ErrorBoundaryDecorator implements UIComponent {
  constructor(private component: UIComponent, private hasError: boolean) {}

  render(): string {
    if (this.hasError) {
      return '&lt;div class="error"&gt;오류가 발생했습니다&lt;/div&gt;'
    }
    return this.component.render()
  }
}

class LoggingDecorator implements UIComponent {
  constructor(private component: UIComponent) {}

  render(): string {
    const result = this.component.render()
    console.log(`[렌더링 로그] ${result}`)
    return result
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>🎨 UI 컴포넌트 상태 설정</h3>

        <div class="form-group">
          <label for="button-label">버튼 라벨:</label>
          <input
            id="button-label"
            v-model="buttonLabel"
            type="text"
            placeholder="예: 제출"
            class="input-field"
          />
        </div>

        <div class="decorator-selection">
          <label>컴포넌트 상태:</label>
          <button
            @click="uiIsLoading = !uiIsLoading"
            :class="['decorator-btn', { active: uiIsLoading }]"
          >
            ⏳ 로딩 중
          </button>
          <button
            @click="uiHasError = !uiHasError"
            :class="['decorator-btn', { active: uiHasError }]"
          >
            ❌ 에러 발생
          </button>
          <button
            @click="uiWithLogging = !uiWithLogging"
            :class="['decorator-btn', { active: uiWithLogging }]"
          >
            📝 로깅 활성화
          </button>
        </div>

        <button @click="renderUI" class="test-btn" :disabled="!buttonLabel">
          컴포넌트 렌더링
        </button>
      </div>

      <div v-if="uiResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ uiResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>패턴의 장점</h2>
      <div class="benefits-list">
        <h3>주요 장점</h3>
        <ul>
          <li><strong>유연한 기능 확장:</strong> 런타임에 선택적으로 기능 적용</li>
          <li><strong>클래스 폭발 방지:</strong> 모든 조합을 위한 서브클래스 불필요</li>
          <li><strong>OCP 준수:</strong> 기존 코드 수정 없이 새 기능 추가</li>
          <li><strong>SRP 준수:</strong> 각 데코레이터가 단일 책임만 가짐</li>
          <li><strong>조합 가능:</strong> 여러 데코레이터를 자유롭게 조합</li>
        </ul>
      </div>

      <div class="code-block">
        <h3>실무 활용 사례</h3>
        <pre><code>// 1. React Higher-Order Components (HOC)
const withAuth = (Component) => (props) => {
  return isAuthenticated ? &lt;Component {...props} /&gt; : &lt;Login /&gt;
}

// 2. Express 미들웨어
app.use(logger)
app.use(authenticate)
app.use(rateLimit)

// 3. Java I/O Streams
new BufferedReader(new FileReader("file.txt"))
new GZIPInputStream(new FileInputStream("file.gz"))

// 4. 데코레이터 패턴 조합 예시
let notifier = new BaseNotifier()
notifier = new EmailNotifier(notifier)
notifier = new SmsNotifier(notifier)
notifier = new SlackNotifier(notifier)
// 기본 + 이메일 + SMS + Slack 모두 실행</code></pre>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

// Interactive variables
const notifierMessage = ref<string>('')
const selectedNotifiers = ref<string[]>([])

const selectedToppings = ref<string[]>([])

const streamData = ref<string>('')
const selectedStreams = ref<string[]>([])

const buttonLabel = ref<string>('')
const uiIsLoading = ref<boolean>(false)
const uiHasError = ref<boolean>(false)
const uiWithLogging = ref<boolean>(false)

// Result variables
const notifierResult = ref<string>('')
const coffeeResult = ref<string>('')
const streamResult = ref<string>('')
const uiResult = ref<string>('')

// ============ A. Notifier Decorator ============

interface Notifier {
  send(message: string): string
}

class BaseNotifier implements Notifier {
  send(message: string): string {
    return `[기본 알림] ${message}`
  }
}

abstract class NotifierDecorator implements Notifier {
  constructor(protected wrappee: Notifier) {}

  send(message: string): string {
    return this.wrappee.send(message)
  }
}

class EmailNotifier extends NotifierDecorator {
  send(message: string): string {
    return super.send(message) + '\n[이메일 발송] ' + message
  }
}

class SmsNotifier extends NotifierDecorator {
  send(message: string): string {
    return super.send(message) + '\n[SMS 발송] ' + message
  }
}

class SlackNotifier extends NotifierDecorator {
  send(message: string): string {
    return super.send(message) + '\n[Slack 발송] ' + message
  }
}

// Interactive functions
function toggleNotifier(type: string) {
  const index = selectedNotifiers.value.indexOf(type)
  if (index > -1) {
    selectedNotifiers.value.splice(index, 1)
  } else {
    selectedNotifiers.value.push(type)
  }
}

function sendNotification() {
  if (!notifierMessage.value) return

  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = notifierResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  let notifier: Notifier = new BaseNotifier()

  // 선택된 데코레이터 적용
  if (selectedNotifiers.value.includes('email')) {
    notifier = new EmailNotifier(notifier)
  }
  if (selectedNotifiers.value.includes('sms')) {
    notifier = new SmsNotifier(notifier)
  }
  if (selectedNotifiers.value.includes('slack')) {
    notifier = new SlackNotifier(notifier)
  }

  const result = notifier.send(notifierMessage.value)

  const channels = ['기본']
  if (selectedNotifiers.value.includes('email')) channels.push('이메일')
  if (selectedNotifiers.value.includes('sms')) channels.push('SMS')
  if (selectedNotifiers.value.includes('slack')) channels.push('Slack')

  notifierResult.value = separator + `[${timestamp}]
메시지: "${notifierMessage.value}"
선택된 채널: ${channels.join(' + ')}

실행 결과:
${result}

✅ 데코레이터 패턴으로 동적으로 알림 채널을 조합했습니다!
💡 런타임에 기능을 선택적으로 추가할 수 있습니다.`
}

// ============ B. Coffee Decorator ============

interface Coffee {
  getDescription(): string
  getCost(): number
}

class SimpleCoffee implements Coffee {
  getDescription(): string {
    return '기본 커피'
  }
  getCost(): number {
    return 2000
  }
}

abstract class CoffeeDecorator implements Coffee {
  constructor(protected coffee: Coffee) {}

  getDescription(): string {
    return this.coffee.getDescription()
  }

  getCost(): number {
    return this.coffee.getCost()
  }
}

class MilkDecorator extends CoffeeDecorator {
  getDescription(): string {
    return this.coffee.getDescription() + ', 우유'
  }
  getCost(): number {
    return this.coffee.getCost() + 500
  }
}

class CaramelDecorator extends CoffeeDecorator {
  getDescription(): string {
    return this.coffee.getDescription() + ', 카라멜 시럽'
  }
  getCost(): number {
    return this.coffee.getCost() + 700
  }
}

class WhippedCreamDecorator extends CoffeeDecorator {
  getDescription(): string {
    return this.coffee.getDescription() + ', 휘핑크림'
  }
  getCost(): number {
    return this.coffee.getCost() + 800
  }
}

function toggleTopping(type: string) {
  const index = selectedToppings.value.indexOf(type)
  if (index > -1) {
    selectedToppings.value.splice(index, 1)
  } else {
    selectedToppings.value.push(type)
  }
}

function orderCoffee() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = coffeeResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  let coffee: Coffee = new SimpleCoffee()

  // 선택된 데코레이터 적용
  if (selectedToppings.value.includes('milk')) {
    coffee = new MilkDecorator(coffee)
  }
  if (selectedToppings.value.includes('caramel')) {
    coffee = new CaramelDecorator(coffee)
  }
  if (selectedToppings.value.includes('whipped')) {
    coffee = new WhippedCreamDecorator(coffee)
  }

  const description = coffee.getDescription()
  const cost = coffee.getCost()

  coffeeResult.value = separator + `[${timestamp}]
주문 내역:
${description}

총 가격: ${cost.toLocaleString()}원

가격 구성:
- 기본 커피: 2,000원
${selectedToppings.value.includes('milk') ? '- 우유: +500원\n' : ''}${selectedToppings.value.includes('caramel') ? '- 카라멜 시럽: +700원\n' : ''}${selectedToppings.value.includes('whipped') ? '- 휘핑크림: +800원\n' : ''}
✅ 각 데코레이터가 가격과 설명을 추가했습니다!
💡 새로운 토핑 추가 시 기존 코드 수정 불필요 (OCP 준수)`
}

// ============ C. Data Stream Decorator ============

interface DataStream {
  writeData(data: string): string
  readData(): string
}

class FileStream implements DataStream {
  private data: string = ''

  writeData(data: string): string {
    this.data = data
    return `파일에 저장: ${data}`
  }

  readData(): string {
    return this.data
  }
}

abstract class StreamDecorator implements DataStream {
  constructor(protected stream: DataStream) {}

  writeData(data: string): string {
    return this.stream.writeData(data)
  }

  readData(): string {
    return this.stream.readData()
  }
}

class CompressionDecorator extends StreamDecorator {
  writeData(data: string): string {
    const compressed = `압축됨[${data}]`
    return `[압축 적용] ${this.stream.writeData(compressed)}`
  }

  readData(): string {
    const data = this.stream.readData()
    return data.replace('압축됨[', '').replace(']', '')
  }
}

class EncryptionDecorator extends StreamDecorator {
  writeData(data: string): string {
    const encrypted = btoa(data)
    return `[암호화 적용] ${this.stream.writeData(encrypted)}`
  }

  readData(): string {
    const data = this.stream.readData()
    try {
      return atob(data)
    } catch {
      return data
    }
  }
}

function toggleStream(type: string) {
  const index = selectedStreams.value.indexOf(type)
  if (index > -1) {
    selectedStreams.value.splice(index, 1)
  } else {
    selectedStreams.value.push(type)
  }
}

function processStream() {
  if (!streamData.value) return

  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = streamResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  let stream: DataStream = new FileStream()

  // 선택된 데코레이터 적용
  if (selectedStreams.value.includes('compression')) {
    stream = new CompressionDecorator(stream)
  }
  if (selectedStreams.value.includes('encryption')) {
    stream = new EncryptionDecorator(stream)
  }

  const writeResult = stream.writeData(streamData.value)
  const readResult = stream.readData()

  const processes = []
  if (selectedStreams.value.includes('compression')) processes.push('압축')
  if (selectedStreams.value.includes('encryption')) processes.push('암호화')

  streamResult.value = separator + `[${timestamp}]
원본 데이터: "${streamData.value}"
적용된 처리: ${processes.length > 0 ? processes.join(' + ') : '없음 (기본 스트림)'}

📝 쓰기 작업:
${writeResult}

📖 읽기 작업:
읽은 데이터: "${readResult}"

✅ 데코레이터를 조합하여 파일 처리를 했습니다!
💡 압축과 암호화를 독립적으로 또는 함께 적용 가능`
}

// ============ D. UI Component Decorator ============

interface UIComponent {
  render(): string
}

class Button implements UIComponent {
  constructor(private label: string) {}

  render(): string {
    return `<button>${this.label}</button>`
  }
}

class LoadingDecorator implements UIComponent {
  constructor(private component: UIComponent, private isLoading: boolean) {}

  render(): string {
    if (this.isLoading) {
      return '<div>로딩 중...</div>'
    }
    return this.component.render()
  }
}

class ErrorBoundaryDecorator implements UIComponent {
  constructor(private component: UIComponent, private hasError: boolean) {}

  render(): string {
    if (this.hasError) {
      return '<div class="error">오류가 발생했습니다</div>'
    }
    return this.component.render()
  }
}

class LoggingDecorator implements UIComponent {
  constructor(private component: UIComponent) {}

  render(): string {
    const result = this.component.render()
    console.log(`[렌더링 로그] ${result}`)
    return result
  }
}

function renderUI() {
  if (!buttonLabel.value) return

  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = uiResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  let component: UIComponent = new Button(buttonLabel.value)

  // 선택된 데코레이터 적용 (순서 중요!)
  if (uiHasError.value) {
    component = new ErrorBoundaryDecorator(component, true)
  } else if (uiIsLoading.value) {
    component = new LoadingDecorator(component, true)
  }

  if (uiWithLogging.value) {
    component = new LoggingDecorator(component)
  }

  const rendered = component.render()

  const appliedDecorators = []
  if (uiHasError.value) appliedDecorators.push('에러 경계')
  else if (uiIsLoading.value) appliedDecorators.push('로딩')
  if (uiWithLogging.value) appliedDecorators.push('로깅')

  uiResult.value = separator + `[${timestamp}]
버튼 라벨: "${buttonLabel.value}"
적용된 데코레이터: ${appliedDecorators.length > 0 ? appliedDecorators.join(' + ') : '없음 (기본 버튼)'}

렌더링 결과:
${rendered}

${uiWithLogging.value ? '💡 콘솔 로그도 확인해보세요!\n' : ''}✅ React HOC와 유사한 패턴으로 UI를 감쌌습니다!
💡 상태에 따라 다른 UI를 렌더링할 수 있습니다.`
}
</script>

<style scoped>
/* DecoratorPattern 전용 스타일이 필요한 경우 여기에 추가 */
/* 공통 스타일은 src/assets/main.css에서 전역으로 적용됨 */
</style>
