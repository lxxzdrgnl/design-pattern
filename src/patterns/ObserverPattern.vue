<template>
  <div class="pattern-container">
    <h1>Observer Pattern</h1>
    <p class="description">
      옵저버 패턴은 하나의 주체 객체(Subject)가 여러 객체(Observers)와 연결되어 있으며,
      주체의 상태가 바뀌면 관련된 모든 옵저버에게 알림이 전달되는 행동 패턴입니다.
    </p>

    <div class="demo-section">
      <h2>A. Stock Price Observer (주식 가격 모니터링)</h2>
      <p>주식 가격이 변경되면 등록된 모든 관찰자에게 자동으로 알림이 전달됩니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Observer 인터페이스
interface Observer {
  update(price: number): void
}

// Subject 인터페이스
interface Subject {
  attach(observer: Observer): void
  detach(observer: Observer): void
  notify(): void
}

// ConcreteSubject - 주식
class Stock implements Subject {
  private observers: Observer[] = []
  private price: number = 0

  attach(observer: Observer): void {
    this.observers.push(observer)
  }

  detach(observer: Observer): void {
    const index = this.observers.indexOf(observer)
    if (index > -1) this.observers.splice(index, 1)
  }

  notify(): void {
    for (const observer of this.observers) {
      observer.update(this.price)
    }
  }

  setPrice(price: number): void {
    this.price = price
    this.notify()
  }
}

// ConcreteObservers
class PriceDisplay implements Observer {
  update(price: number): void {
    console.log(`[가격 표시] 현재 가격: ${price}원`)
  }
}

class PriceAlert implements Observer {
  constructor(private threshold: number) {}

  update(price: number): void {
    if (price > this.threshold) {
      console.log(`[가격 알림] 목표가 도달! ${price}원`)
    }
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <div class="form-group">
          <label for="stock-price">주식 가격:</label>
          <input
            id="stock-price"
            v-model.number="stockPrice"
            type="number"
            class="input-field"
            placeholder="예: 15000"
          />
        </div>
        <div class="form-group">
          <label for="alert-threshold">알림 목표가:</label>
          <input
            id="alert-threshold"
            v-model.number="alertThreshold"
            type="number"
            class="input-field"
            placeholder="예: 20000"
          />
        </div>
        <button @click="updateStockPrice" class="test-btn">가격 변경</button>
      </div>

      <div v-if="stockResult" class="result">
        <h3>실행 결과</h3>
        <pre>{{ stockResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>B. Weather Station (날씨 관측소)</h2>
      <p>날씨 데이터가 변경되면 여러 디스플레이가 자동으로 업데이트됩니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Weather Subject
class WeatherStation implements Subject {
  private observers: Observer[] = []
  private temperature: number = 0
  private humidity: number = 0

  attach(observer: Observer): void {
    this.observers.push(observer)
  }

  detach(observer: Observer): void {
    const index = this.observers.indexOf(observer)
    if (index > -1) this.observers.splice(index, 1)
  }

  notify(): void {
    for (const observer of this.observers) {
      observer.update(this.temperature, this.humidity)
    }
  }

  setMeasurements(temp: number, humidity: number): void {
    this.temperature = temp
    this.humidity = humidity
    this.notify()
  }
}

// Weather Observers
class CurrentConditionsDisplay implements Observer {
  update(temp: number, humidity: number): void {
    console.log(`[현재 날씨] 온도: ${temp}°C, 습도: ${humidity}%`)
  }
}

class StatisticsDisplay implements Observer {
  private temps: number[] = []

  update(temp: number): void {
    this.temps.push(temp)
    const avg = this.temps.reduce((a, b) => a + b, 0) / this.temps.length
    console.log(`[통계] 평균 온도: ${avg.toFixed(1)}°C`)
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <div class="form-group">
          <label for="temperature">온도 (°C):</label>
          <input
            id="temperature"
            v-model.number="temperature"
            type="number"
            class="input-field"
            placeholder="예: 25"
          />
        </div>
        <div class="form-group">
          <label for="humidity">습도 (%):</label>
          <input
            id="humidity"
            v-model.number="humidity"
            type="number"
            class="input-field"
            placeholder="예: 65"
          />
        </div>
        <button @click="updateWeatherData" class="test-btn">날씨 업데이트</button>
      </div>

      <div v-if="weatherResult" class="result">
        <h3>실행 결과</h3>
        <pre>{{ weatherResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>C. Event System (이벤트 시스템)</h2>
      <p>Node.js EventEmitter와 유사한 이벤트 발행-구독 시스템입니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// EventEmitter (Pub/Sub 패턴)
class EventEmitter {
  private listeners: Map&lt;string, Function[]&gt; = new Map()

  on(event: string, callback: Function): void {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, [])
    }
    this.listeners.get(event)!.push(callback)
  }

  off(event: string, callback: Function): void {
    const callbacks = this.listeners.get(event)
    if (callbacks) {
      const index = callbacks.indexOf(callback)
      if (index > -1) callbacks.splice(index, 1)
    }
  }

  emit(event: string, ...args: any[]): void {
    const callbacks = this.listeners.get(event)
    if (callbacks) {
      callbacks.forEach(callback => callback(...args))
    }
  }
}

// 사용 예시
const emitter = new EventEmitter()

emitter.on('userLogin', (username: string) => {
  console.log(`[이벤트] ${username}님이 로그인했습니다`)
})

emitter.on('userLogin', (username: string) => {
  console.log(`[분석] 로그인 이벤트 기록: ${username}`)
})

emitter.emit('userLogin', 'John')</code></pre>
      </div>

      <div class="interactive-demo">
        <div class="form-group">
          <label for="event-name">이벤트 이름:</label>
          <input
            id="event-name"
            v-model="eventName"
            type="text"
            class="input-field"
            placeholder="예: userLogin"
          />
        </div>
        <div class="form-group">
          <label for="event-data">이벤트 데이터:</label>
          <input
            id="event-data"
            v-model="eventData"
            type="text"
            class="input-field"
            placeholder="예: John"
          />
        </div>
        <button @click="emitEvent" class="test-btn">이벤트 발생</button>
      </div>

      <div v-if="eventResult" class="result">
        <h3>실행 결과</h3>
        <pre>{{ eventResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>D. YouTube Channel (구독 시스템)</h2>
      <p>유튜브 채널의 구독자에게 새 동영상 알림을 보내는 시스템입니다.</p>

      <div class="code-block">
        <h3>코드</h3>
        <pre><code>// Observer - 구독자
interface Subscriber {
  notify(channelName: string, videoTitle: string): void
}

// Subject - 채널
class YouTubeChannel {
  private subscribers: Subscriber[] = []
  private channelName: string

  constructor(name: string) {
    this.channelName = name
  }

  subscribe(subscriber: Subscriber): void {
    this.subscribers.push(subscriber)
  }

  unsubscribe(subscriber: Subscriber): void {
    const index = this.subscribers.indexOf(subscriber)
    if (index > -1) this.subscribers.splice(index, 1)
  }

  uploadVideo(title: string): void {
    console.log(`[${this.channelName}] 새 동영상 업로드: ${title}`)
    this.notifySubscribers(title)
  }

  private notifySubscribers(title: string): void {
    for (const subscriber of this.subscribers) {
      subscriber.notify(this.channelName, title)
    }
  }
}

// ConcreteObserver - 구독자
class User implements Subscriber {
  constructor(private name: string) {}

  notify(channelName: string, videoTitle: string): void {
    console.log(
      `[알림] ${this.name}님, ${channelName}에서 새 동영상을 업로드했습니다: "${videoTitle}"`
    )
  }
}</code></pre>
      </div>

      <div class="interactive-demo">
        <div class="form-group">
          <label for="channel-name">채널 이름:</label>
          <input
            id="channel-name"
            v-model="channelName"
            type="text"
            class="input-field"
            placeholder="예: 코딩 채널"
          />
        </div>
        <div class="form-group">
          <label for="video-title">동영상 제목:</label>
          <input
            id="video-title"
            v-model="videoTitle"
            type="text"
            class="input-field"
            placeholder="예: TypeScript 기초 강좌"
          />
        </div>
        <button @click="uploadYouTubeVideo" class="test-btn">동영상 업로드</button>
      </div>

      <div v-if="youtubeResult" class="result">
        <h3>실행 결과</h3>
        <pre>{{ youtubeResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>패턴의 장점</h2>
      <div class="benefits-list">
        <h3>주요 장점</h3>
        <ul>
          <li><strong>느슨한 결합:</strong> Subject와 Observer가 독립적으로 변경 가능</li>
          <li><strong>OCP 준수:</strong> 새로운 옵저버 추가 시 Subject 수정 불필요</li>
          <li><strong>동적 구독:</strong> 런타임에 옵저버를 추가/제거 가능</li>
          <li><strong>브로드캐스트:</strong> 한 번의 변경으로 여러 객체에 알림</li>
          <li><strong>이벤트 기반:</strong> 비동기 이벤트 처리에 적합</li>
        </ul>
      </div>

      <div class="code-block">
        <h3>실무 활용 사례</h3>
        <pre><code>// 1. DOM 이벤트 리스너
button.addEventListener('click', handleClick)
document.addEventListener('scroll', handleScroll)

// 2. Node.js EventEmitter
const EventEmitter = require('events')
server.on('connection', (socket) => { })

// 3. RxJS Observable
observable.subscribe({
  next: (value) => console.log(value),
  error: (err) => console.error(err),
  complete: () => console.log('done')
})

// 4. Vue/React 상태 관리
// Vuex: store.subscribe((mutation, state) => {})
// Redux: store.subscribe(() => {})

// 5. MVC 패턴
// Model이 변경되면 View에 자동으로 알림</code></pre>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const stockResult = ref<string>('')
const weatherResult = ref<string>('')
const eventResult = ref<string>('')
const youtubeResult = ref<string>('')

// Interactive variables
const stockPrice = ref<number>(15000)
const alertThreshold = ref<number>(20000)
const temperature = ref<number>(25)
const humidity = ref<number>(65)
const eventName = ref<string>('userLogin')
const eventData = ref<string>('John')
const channelName = ref<string>('코딩 채널')
const videoTitle = ref<string>('TypeScript 기초 강좌')

// Persistent objects for interactive mode
let persistentStock: Stock | null = null
let persistentDisplay: PriceDisplay | null = null
let persistentAlert: PriceAlert | null = null

let persistentWeatherStation: WeatherStation | null = null
let persistentCurrentDisplay: CurrentConditionsDisplay | null = null
let persistentStatsDisplay: StatisticsDisplay | null = null

let persistentEmitter: EventEmitter | null = null

let persistentChannel: YouTubeChannel | null = null
let persistentUsers: User[] = []

// ============ A. Stock Price Observer ============

interface StockObserver {
  update(price: number): void
}

interface StockSubject {
  attach(observer: StockObserver): void
  detach(observer: StockObserver): void
  notify(): void
}

class Stock implements StockSubject {
  private observers: StockObserver[] = []
  private price: number = 0

  attach(observer: StockObserver): void {
    this.observers.push(observer)
  }

  detach(observer: StockObserver): void {
    const index = this.observers.indexOf(observer)
    if (index > -1) this.observers.splice(index, 1)
  }

  notify(): void {
    for (const observer of this.observers) {
      observer.update(this.price)
    }
  }

  setPrice(price: number): void {
    this.price = price
    this.notify()
  }

  getPrice(): number {
    return this.price
  }
}

class PriceDisplay implements StockObserver {
  private logs: string[] = []

  update(price: number): void {
    this.logs.push(`[가격 표시] 현재 가격: ${price}원`)
  }

  getLogs(): string[] {
    return this.logs
  }
}

class PriceAlert implements StockObserver {
  private logs: string[] = []

  constructor(private threshold: number) {}

  update(price: number): void {
    if (price > this.threshold) {
      this.logs.push(`[가격 알림] 목표가 도달! ${price}원 (목표: ${this.threshold}원)`)
    }
  }

  getLogs(): string[] {
    return this.logs
  }
}

// Interactive function for Stock Observer
function updateStockPrice() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = stockResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  // Initialize observers on first run
  if (!persistentStock) {
    persistentStock = new Stock()
    persistentDisplay = new PriceDisplay()
    persistentAlert = new PriceAlert(alertThreshold.value)

    persistentStock.attach(persistentDisplay)
    persistentStock.attach(persistentAlert)
  }

  // Update threshold if changed
  if (persistentAlert && persistentAlert instanceof PriceAlert) {
    persistentAlert = new PriceAlert(alertThreshold.value)
    persistentStock.detach(persistentAlert)
    persistentStock.attach(persistentAlert)
  }

  // Set new price
  persistentStock.setPrice(stockPrice.value)

  const displayLogs = persistentDisplay!.getLogs()
  const alertLogs = persistentAlert!.getLogs()
  const lastDisplay = displayLogs[displayLogs.length - 1] || '(알림 없음)'
  const lastAlert = alertLogs[alertLogs.length - 1] || '(목표가 미도달)'

  stockResult.value = separator + `[${timestamp}]
주식 가격 변경: ${stockPrice.value}원

${lastDisplay}
${lastAlert}

✅ 모든 옵저버가 자동으로 알림을 받았습니다
💡 Observer 패턴: Subject의 상태 변경 시 등록된 모든 Observer에게 자동 알림`
}

function testStockObserver() {
  const stock = new Stock()
  const display = new PriceDisplay()
  const alert = new PriceAlert(15000)

  // 옵저버 등록
  stock.attach(display)
  stock.attach(alert)

  const logs: string[] = []
  logs.push('=== 주식 가격 모니터링 시작 ===\n')

  // 가격 변경 1
  logs.push('가격 변경: 10000원')
  stock.setPrice(10000)

  // 가격 변경 2
  logs.push('가격 변경: 12000원')
  stock.setPrice(12000)

  // 가격 변경 3 (목표가 도달)
  logs.push('가격 변경: 16000원')
  stock.setPrice(16000)

  // 가격 변경 4
  logs.push('가격 변경: 18000원')
  stock.setPrice(18000)

  logs.push('\n=== 옵저버 알림 내역 ===\n')
  logs.push(...display.getLogs())
  logs.push('')
  logs.push(...alert.getLogs())

  logs.push('\n모든 옵저버가 자동으로 업데이트되었습니다!')

  stockResult.value = logs.join('\n')
}

// ============ B. Weather Station ============

interface WeatherObserver {
  update(temp: number, humidity: number): void
}

class WeatherStation {
  private observers: WeatherObserver[] = []
  private temperature: number = 0
  private humidity: number = 0

  attach(observer: WeatherObserver): void {
    this.observers.push(observer)
  }

  detach(observer: WeatherObserver): void {
    const index = this.observers.indexOf(observer)
    if (index > -1) this.observers.splice(index, 1)
  }

  notify(): void {
    for (const observer of this.observers) {
      observer.update(this.temperature, this.humidity)
    }
  }

  setMeasurements(temp: number, humidity: number): void {
    this.temperature = temp
    this.humidity = humidity
    this.notify()
  }
}

class CurrentConditionsDisplay implements WeatherObserver {
  private logs: string[] = []

  update(temp: number, humidity: number): void {
    this.logs.push(`[현재 날씨] 온도: ${temp}°C, 습도: ${humidity}%`)
  }

  getLogs(): string[] {
    return this.logs
  }
}

class StatisticsDisplay implements WeatherObserver {
  private temps: number[] = []
  private logs: string[] = []

  update(temp: number): void {
    this.temps.push(temp)
    const avg = this.temps.reduce((a, b) => a + b, 0) / this.temps.length
    const min = Math.min(...this.temps)
    const max = Math.max(...this.temps)
    this.logs.push(`[통계] 평균: ${avg.toFixed(1)}°C, 최저: ${min}°C, 최고: ${max}°C`)
  }

  getLogs(): string[] {
    return this.logs
  }
}

// Interactive function for Weather Station
function updateWeatherData() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = weatherResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  // Initialize on first run
  if (!persistentWeatherStation) {
    persistentWeatherStation = new WeatherStation()
    persistentCurrentDisplay = new CurrentConditionsDisplay()
    persistentStatsDisplay = new StatisticsDisplay()

    persistentWeatherStation.attach(persistentCurrentDisplay)
    persistentWeatherStation.attach(persistentStatsDisplay)
  }

  // Update measurements
  persistentWeatherStation.setMeasurements(temperature.value, humidity.value)

  const currentLogs = persistentCurrentDisplay!.getLogs()
  const statsLogs = persistentStatsDisplay!.getLogs()
  const lastCurrent = currentLogs[currentLogs.length - 1] || '(데이터 없음)'
  const lastStats = statsLogs[statsLogs.length - 1] || '(통계 없음)'

  weatherResult.value = separator + `[${timestamp}]
날씨 데이터 업데이트: ${temperature.value}°C, ${humidity.value}%

${lastCurrent}
${lastStats}

✅ 모든 디스플레이가 동시에 업데이트되었습니다
💡 Observer 패턴: 하나의 데이터 변경으로 여러 옵저버가 자동 갱신`
}

function testWeatherObserver() {
  const station = new WeatherStation()
  const currentDisplay = new CurrentConditionsDisplay()
  const statsDisplay = new StatisticsDisplay()

  station.attach(currentDisplay)
  station.attach(statsDisplay)

  const logs: string[] = []
  logs.push('=== 날씨 관측소 시작 ===\n')

  logs.push('측정 1: 25°C, 65%')
  station.setMeasurements(25, 65)

  logs.push('측정 2: 27°C, 70%')
  station.setMeasurements(27, 70)

  logs.push('측정 3: 23°C, 60%')
  station.setMeasurements(23, 60)

  logs.push('\n=== 현재 날씨 디스플레이 ===\n')
  logs.push(...currentDisplay.getLogs())

  logs.push('\n=== 통계 디스플레이 ===\n')
  logs.push(...statsDisplay.getLogs())

  logs.push('\n여러 디스플레이가 동시에 업데이트됩니다!')

  weatherResult.value = logs.join('\n')
}

// ============ C. Event System ============

class EventEmitter {
  private listeners: Map<string, Function[]> = new Map()

  on(event: string, callback: Function): void {
    if (!this.listeners.has(event)) {
      this.listeners.set(event, [])
    }
    this.listeners.get(event)!.push(callback)
  }

  off(event: string, callback: Function): void {
    const callbacks = this.listeners.get(event)
    if (callbacks) {
      const index = callbacks.indexOf(callback)
      if (index > -1) callbacks.splice(index, 1)
    }
  }

  emit(event: string, ...args: any[]): void {
    const callbacks = this.listeners.get(event)
    if (callbacks) {
      callbacks.forEach(callback => callback(...args))
    }
  }
}

// Interactive function for Event System
function emitEvent() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = eventResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const logs: string[] = []

  // Initialize emitter on first run
  if (!persistentEmitter) {
    persistentEmitter = new EventEmitter()

    // Register listeners
    persistentEmitter.on('userLogin', (username: string) => {
      logs.push(`[이벤트] ${username}님이 로그인했습니다`)
    })

    persistentEmitter.on('userLogin', (username: string) => {
      logs.push(`[분석] 로그인 이벤트 기록: ${username}`)
    })

    persistentEmitter.on('userLogin', (username: string) => {
      logs.push(`[환영] ${username}님 환영합니다!`)
    })

    persistentEmitter.on('userLogout', (username: string) => {
      logs.push(`[이벤트] ${username}님이 로그아웃했습니다`)
    })

    persistentEmitter.on('dataUpdate', (data: string) => {
      logs.push(`[데이터] 업데이트: ${data}`)
    })
  }

  // Clear logs array for this emission
  logs.length = 0

  // Emit the event
  persistentEmitter.emit(eventName.value, eventData.value)

  const result = logs.length > 0 ? logs.join('\n') : `(이벤트 "${eventName.value}"에 등록된 리스너가 없습니다)`

  eventResult.value = separator + `[${timestamp}]
이벤트 발생: emit("${eventName.value}", "${eventData.value}")

${result}

✅ 한 번의 emit으로 모든 리스너가 실행되었습니다
💡 Observer 패턴 (Pub/Sub): 이벤트 기반 비동기 처리에 최적화`
}

function testEventSystem() {
  const emitter = new EventEmitter()
  const logs: string[] = []

  logs.push('=== 이벤트 시스템 테스트 ===\n')

  // 이벤트 리스너 등록
  const loginHandler = (username: string) => {
    logs.push(`[이벤트] ${username}님이 로그인했습니다`)
  }

  const analyticsHandler = (username: string) => {
    logs.push(`[분석] 로그인 이벤트 기록: ${username}`)
  }

  const welcomeHandler = (username: string) => {
    logs.push(`[환영] ${username}님 환영합니다!`)
  }

  emitter.on('userLogin', loginHandler)
  emitter.on('userLogin', analyticsHandler)
  emitter.on('userLogin', welcomeHandler)

  logs.push('3개의 리스너 등록 완료\n')

  // 이벤트 발생
  logs.push('emit("userLogin", "John"):')
  emitter.emit('userLogin', 'John')

  logs.push('\nemit("userLogin", "Alice"):')
  emitter.emit('userLogin', 'Alice')

  logs.push('\n한 번의 emit으로 모든 리스너가 실행됩니다!')

  eventResult.value = logs.join('\n')
}

// ============ D. YouTube Channel ============

interface Subscriber {
  notify(channelName: string, videoTitle: string): void
}

class YouTubeChannel {
  private subscribers: Subscriber[] = []
  private channelName: string

  constructor(name: string) {
    this.channelName = name
  }

  subscribe(subscriber: Subscriber): void {
    this.subscribers.push(subscriber)
  }

  unsubscribe(subscriber: Subscriber): void {
    const index = this.subscribers.indexOf(subscriber)
    if (index > -1) this.subscribers.splice(index, 1)
  }

  uploadVideo(title: string): string[] {
    const logs: string[] = []
    logs.push(`[${this.channelName}] 새 동영상 업로드: "${title}"`)
    this.notifySubscribers(title, logs)
    return logs
  }

  private notifySubscribers(title: string, logs: string[]): void {
    for (const subscriber of this.subscribers) {
      subscriber.notify(this.channelName, title)
    }
  }

  getSubscriberCount(): number {
    return this.subscribers.length
  }
}

class User implements Subscriber {
  private notifications: string[] = []

  constructor(private name: string) {}

  notify(channelName: string, videoTitle: string): void {
    const msg = `${this.name}님, ${channelName}에서 새 동영상: "${videoTitle}"`
    this.notifications.push(msg)
  }

  getNotifications(): string[] {
    return this.notifications
  }

  getName(): string {
    return this.name
  }
}

// Interactive function for YouTube Channel
function uploadYouTubeVideo() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = youtubeResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  // Initialize channel and users on first run
  if (!persistentChannel) {
    persistentChannel = new YouTubeChannel(channelName.value)
    persistentUsers = [
      new User('철수'),
      new User('영희'),
      new User('민수')
    ]

    persistentUsers.forEach(user => persistentChannel!.subscribe(user))
  }

  // Update channel name if changed
  if (persistentChannel && persistentChannel instanceof YouTubeChannel) {
    // Create new channel with updated name if it changed
    const currentName = channelName.value
    persistentChannel = new YouTubeChannel(currentName)
    persistentUsers.forEach(user => persistentChannel!.subscribe(user))
  }

  // Upload video
  const uploadLogs = persistentChannel.uploadVideo(videoTitle.value)

  const logs: string[] = []
  logs.push(`채널: ${channelName.value}`)
  logs.push(`구독자: ${persistentChannel.getSubscriberCount()}명`)
  logs.push('')
  logs.push(...uploadLogs)
  logs.push('')
  logs.push('=== 구독자 알림 ===')

  persistentUsers.forEach(user => {
    const userNotifications = user.getNotifications()
    const lastNotification = userNotifications[userNotifications.length - 1]
    if (lastNotification) {
      logs.push(`[${user.getName()}] ${lastNotification}`)
    }
  })

  youtubeResult.value = separator + `[${timestamp}]
${logs.join('\n')}

✅ 모든 구독자가 알림을 받았습니다
💡 Observer 패턴: 구독 시스템의 핵심 원리`
}

function testYouTubeObserver() {
  const channel = new YouTubeChannel('코딩 채널')

  const user1 = new User('철수')
  const user2 = new User('영희')
  const user3 = new User('민수')

  const logs: string[] = []
  logs.push('=== 유튜브 구독 시스템 ===\n')

  // 구독자 추가
  channel.subscribe(user1)
  channel.subscribe(user2)
  channel.subscribe(user3)
  logs.push(`구독자 ${channel.getSubscriberCount()}명 등록\n`)

  // 동영상 1 업로드
  logs.push('--- 동영상 1 업로드 ---')
  const video1Logs = channel.uploadVideo('TypeScript 기초 강좌')
  logs.push(...video1Logs)
  logs.push('')

  // 동영상 2 업로드
  logs.push('--- 동영상 2 업로드 ---')
  const video2Logs = channel.uploadVideo('디자인 패턴 완벽 정리')
  logs.push(...video2Logs)
  logs.push('')

  // 각 사용자의 알림 확인
  logs.push('=== 사용자별 알림 내역 ===\n')
  logs.push(`${user1.getName()}의 알림:`)
  logs.push(...user1.getNotifications().map(n => `  - ${n}`))
  logs.push('')

  logs.push(`${user2.getName()}의 알림:`)
  logs.push(...user2.getNotifications().map(n => `  - ${n}`))
  logs.push('')

  logs.push(`${user3.getName()}의 알림:`)
  logs.push(...user3.getNotifications().map(n => `  - ${n}`))
  logs.push('')

  logs.push('모든 구독자가 알림을 받았습니다!')

  youtubeResult.value = logs.join('\n')
}
</script>

<style scoped>
/* ObserverPattern 전용 스타일이 필요한 경우 여기에 추가 */
/* 공통 스타일은 src/assets/main.css에서 전역으로 적용됨 */
</style>
