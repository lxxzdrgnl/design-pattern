<template>
  <div class="pattern-container">
    <h1>Singleton Pattern</h1>
    <p class="description">
      싱글톤 패턴은 클래스의 인스턴스가 하나만 생성되도록 보장하고,
      어디서나 그 인스턴스에 접근할 수 있도록 하는 디자인 패턴입니다.
    </p>

    <div class="demo-section">
      <h2>A. Eager Initialization (즉시 초기화)</h2>
      <p>클래스가 로드될 때 즉시 인스턴스를 생성합니다.</p>

      <div class="code-block">
        <h3>📝 코드</h3>
        <pre><code>class EagerSingleton {
  private static readonly instance: EagerSingleton = new EagerSingleton()
  private constructor() {}

  public static getInstance(): EagerSingleton {
    return EagerSingleton.instance
  }

  public sayHello(): string {
    return '안녕하세요! 저는 싱글톤 인스턴스입니다 👋'
  }
}

const s1 = EagerSingleton.getInstance()
const s2 = EagerSingleton.getInstance()
console.log(s1 === s2) // true</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>🔒 인스턴스 생성 및 비교</h3>
        <p>여러 번 getInstance()를 호출해도 동일한 인스턴스가 반환됩니다.</p>

        <div class="form-group">
          <label>인스턴스 요청 횟수:</label>
          <input v-model.number="eagerRequestCount" type="number" min="2" max="10" placeholder="2-10" class="input-field" />
        </div>

        <button
          @click="testEagerInstances"
          class="test-btn"
          :disabled="!eagerRequestCount || eagerRequestCount < 2"
        >
          getInstance() 여러 번 호출
        </button>
      </div>

      <div v-if="eagerResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ eagerResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>B. Static Block Initialization (정적 블록)</h2>
      <p>정적 블록을 사용하여 예외 처리가 가능한 초기화를 수행합니다.</p>

      <div class="code-block">
        <h3>📝 코드</h3>
        <pre><code>class StaticBlockSingleton {
  private static instance: StaticBlockSingleton
  private constructor() {}

  static {
    try {
      StaticBlockSingleton.instance = new StaticBlockSingleton()
    } catch (error) {
      throw new Error('싱글톤 객체 생성 오류')
    }
  }

  public static getInstance(): StaticBlockSingleton {
    return StaticBlockSingleton.instance
  }

  public getInfo(): string {
    return '정적 블록으로 초기화된 싱글톤입니다 🔧'
  }
}

const s1 = StaticBlockSingleton.getInstance()
console.log(s1 === StaticBlockSingleton.getInstance()) // true</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>🔧 인스턴스 생성 및 getInfo() 호출</h3>
        <p>정적 블록으로 초기화된 싱글톤의 메서드를 호출합니다.</p>

        <button @click="testStaticBlockInstances" class="test-btn">
          getInstance() 호출 및 getInfo() 실행
        </button>
      </div>

      <div v-if="staticBlockResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ staticBlockResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>C. Lazy Initialization (지연 초기화)</h2>
      <p>첫 번째 getInstance() 호출 시점에 인스턴스를 생성합니다.</p>

      <div class="code-block">
        <h3>📝 코드</h3>
        <pre><code>class LazySingleton {
  private static instance: LazySingleton | null = null
  private createdAt: Date

  private constructor() {
    this.createdAt = new Date()
  }

  public static getInstance(): LazySingleton {
    if (LazySingleton.instance === null) {
      LazySingleton.instance = new LazySingleton()
    }
    return LazySingleton.instance
  }

  public getCreatedTime(): string {
    return `인스턴스 생성 시간: ${this.createdAt.toLocaleTimeString('ko-KR', { timeZone: 'Asia/Seoul' })}`
  }
}

const a = LazySingleton.getInstance()
const b = LazySingleton.getInstance()
console.log(a === b) // true</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>⏰ 지연 초기화 테스트</h3>
        <p>첫 번째 getInstance() 호출 시점에 인스턴스가 생성되고 생성 시간이 기록됩니다.</p>

        <button @click="testLazyInstances" class="test-btn">
          getInstance() 호출 및 생성 시간 확인
        </button>
      </div>

      <div v-if="lazyResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ lazyResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>D. Module-Based Pattern (모듈 기반)</h2>
      <p>모듈 캐싱을 활용하여 내보낸 인스턴스를 싱글톤으로 사용합니다.</p>

      <div class="code-block">
        <h3>📝 코드</h3>
        <pre><code>class Service {
  public ping(): string {
    return 'pong'
  }

  public getStatus(): string {
    return '모듈 기반 싱글톤 서비스 실행 중 ⚡'
  }
}

// 모듈 스코프에서 인스턴스를 생성하여 export
export const service = new Service()

// 모듈이 한 번만 로드되므로 자동으로 싱글톤</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>⚡ 모듈 기반 싱글톤 테스트</h3>
        <p>모듈 스코프에서 생성된 인스턴스의 메서드를 호출합니다.</p>

        <button @click="testModuleInstances" class="test-btn">
          ping() 및 getStatus() 호출
        </button>
      </div>

      <div v-if="moduleResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ moduleResult }}</pre>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const eagerRequestCount = ref<number | null>(null)
const eagerResult = ref<string>('')
const staticBlockResult = ref<string>('')
const lazyResult = ref<string>('')
const moduleResult = ref<string>('')

// A. Eager Initialization
class EagerSingleton {
  private static readonly instance: EagerSingleton = new EagerSingleton()
  private constructor() {}
  public static getInstance(): EagerSingleton {
    return EagerSingleton.instance
  }
  public sayHello(): string {
    return '안녕하세요! 저는 싱글톤 인스턴스입니다 👋'
  }
}

function testEagerInstances() {
  if (!eagerRequestCount.value) return

  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = eagerResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const instances: EagerSingleton[] = []
  for (let i = 0; i < eagerRequestCount.value; i++) {
    instances.push(EagerSingleton.getInstance())
  }

  const hello = instances[0]?.sayHello() || ''
  const comparisons = instances.slice(1).map((inst, idx) =>
    `s${idx + 2} === s1: ${instances[0] === inst}`
  ).join('\n')

  eagerResult.value = separator + `[${timestamp}]
${eagerRequestCount.value}번의 getInstance() 호출

첫 번째 인스턴스의 sayHello():
${hello}

인스턴스 비교:
${comparisons}

✅ 모든 인스턴스가 동일합니다!
💡 Eager Initialization은 클래스 로드 시 즉시 생성됩니다.`
}

// B. Static Block Initialization
class StaticBlockSingleton {
  private static instance: StaticBlockSingleton
  private constructor() {}

  static {
    try {
      StaticBlockSingleton.instance = new StaticBlockSingleton()
    } catch (error) {
      throw new Error('싱글톤 객체 생성 오류')
    }
  }

  public static getInstance(): StaticBlockSingleton {
    return StaticBlockSingleton.instance
  }

  public getInfo(): string {
    return '정적 블록으로 초기화된 싱글톤입니다 🔧'
  }
}

function testStaticBlockInstances() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = staticBlockResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const s1 = StaticBlockSingleton.getInstance()
  const s2 = StaticBlockSingleton.getInstance()
  const info = s1.getInfo()
  const isSame = s1 === s2

  staticBlockResult.value = separator + `[${timestamp}]
getInstance() 두 번 호출

s1.getInfo():
${info}

인스턴스 비교:
s1 === s2: ${isSame}

✅ 정적 블록에서 예외 처리가 가능합니다!
💡 static { } 블록 내에서 try-catch 사용 가능`
}

// C. Lazy Initialization
class LazySingleton {
  private static instance: LazySingleton | null = null
  private createdAt: Date

  private constructor() {
    this.createdAt = new Date()
  }

  public static getInstance(): LazySingleton {
    if (LazySingleton.instance === null) {
      LazySingleton.instance = new LazySingleton()
    }
    return LazySingleton.instance
  }

  public getCreatedTime(): string {
    return `인스턴스 생성 시간: ${this.createdAt.toLocaleTimeString('ko-KR', { timeZone: 'Asia/Seoul' })}`
  }
}

function testLazyInstances() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = lazyResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const a = LazySingleton.getInstance()
  const b = LazySingleton.getInstance()
  const createdTime = a.getCreatedTime()
  const isSame = a === b

  lazyResult.value = separator + `[${timestamp}]
getInstance() 두 번 호출

${createdTime}

인스턴스 비교:
a === b: ${isSame}

✅ 첫 번째 getInstance() 호출 시점에 생성됩니다!
💡 필요할 때까지 인스턴스 생성을 지연시킵니다.`
}

// D. Module-Based Pattern
class Service {
  public ping(): string {
    return 'pong'
  }

  public getStatus(): string {
    return '모듈 기반 싱글톤 서비스 실행 중 ⚡'
  }
}

const serviceInstance = new Service()

function testModuleInstances() {
  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = moduleResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const response1 = serviceInstance.ping()
  const response2 = serviceInstance.ping()
  const status = serviceInstance.getStatus()

  moduleResult.value = separator + `[${timestamp}]
서비스 메서드 호출

getStatus():
${status}

ping() 호출:
1차: ${response1}
2차: ${response2}

✅ 모듈 스코프에서 export된 인스턴스는 자동으로 싱글톤!
💡 모듈이 한 번만 로드되므로 캐싱됩니다.`
}
</script>

<style scoped>
/* SingletonPattern 전용 스타일이 필요한 경우 여기에 추가 */
/* 공통 스타일은 src/assets/main.css에서 전역으로 적용됨 */
</style>
