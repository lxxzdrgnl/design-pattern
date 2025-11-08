<template>
  <div class="pattern-container">
    <h1>Adapter Pattern</h1>
    <p class="description">
      어댑터 패턴은 서로 호환되지 않는 인터페이스를 가진 객체들을 함께 동작시킬 수 있도록
      중간 변환기 역할을 수행하는 구조 패턴입니다. 전기 콘센트 어댑터처럼 서로 다른 규격을 연결합니다.
    </p>

    <div class="demo-section">
      <h2>A. Payment Gateway Adapter - 인터랙티브 데모</h2>
      <p>기존 결제 시스템(Target)과 새로운 결제 게이트웨이(Adaptee)를 연결합니다.</p>

      <div class="code-block">
        <h3>📝 코드</h3>
        <pre><code>// Target - 기존 시스템이 기대하는 인터페이스
interface OldPaymentProcessor {
  pay(amount: number): string
}

// Adaptee - 새로운 결제 게이트웨이 (호환되지 않는 인터페이스)
class NewPaymentGateway {
  makePayment(value: number, currency: string): string {
    return `New Gateway: ${value} ${currency} 결제 완료`
  }

  getTransactionFee(value: number): number {
    return value * 0.02 // 2% 수수료
  }
}

// Adapter - 두 인터페이스를 연결하는 어댑터
class PaymentAdapter implements OldPaymentProcessor {
  constructor(private gateway: NewPaymentGateway) {}

  pay(amount: number): string {
    // Target의 pay()를 Adaptee의 makePayment()로 변환
    const fee = this.gateway.getTransactionFee(amount)
    const result = this.gateway.makePayment(amount, 'KRW')
    return `${result} (수수료: ${fee}원)`
  }
}

// 클라이언트 코드는 Target 인터페이스만 사용
function processPayment(processor: OldPaymentProcessor, amount: number) {
  return processor.pay(amount)
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>💳 결제 금액 입력</h3>
        <div class="form-group">
          <label for="payment-amount">결제 금액 (원):</label>
          <input
            id="payment-amount"
            v-model.number="paymentAmount"
            type="number"
            placeholder="예: 100000"
            min="1000"
            step="1000"
            class="input-field"
          />
        </div>
        <button @click="testPaymentAdapter" class="test-btn" :disabled="!paymentAmount || paymentAmount < 1000">
          결제 처리
        </button>
      </div>

      <div v-if="paymentResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ paymentResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>B. Logger Adapter - 인터랙티브 데모</h2>
      <p>오래된 로깅 시스템을 새로운 구조화된 로거로 어댑터를 통해 사용합니다.</p>

      <div class="code-block">
        <h3>📝 코드</h3>
        <pre><code>// Target - 레거시 로거 인터페이스
interface LegacyLogger {
  log(message: string): void
}

// Adaptee - 새로운 구조화된 로거
class ModernStructuredLogger {
  logWithMetadata(level: string, message: string, metadata: object): string {
    const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
    return `[${timestamp}] ${level.toUpperCase()}: ${message} | ${JSON.stringify(metadata)}`
  }
}

// Adapter - 레거시 인터페이스를 새 로거에 연결
class LoggerAdapter implements LegacyLogger {
  constructor(private modernLogger: ModernStructuredLogger) {}

  log(message: string): string {
    // 간단한 log()를 구조화된 로그로 변환
    return this.modernLogger.logWithMetadata(
      'info',
      message,
      { source: 'legacy-system', timestamp: Date.now() }
    )
  }
}

// 레거시 코드는 변경 없이 그대로 사용
function legacySystemLog(logger: LegacyLogger, msg: string) {
  return logger.log(msg)
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>📝 로그 메시지 입력</h3>
        <div class="form-group">
          <label for="log-message">로그 메시지:</label>
          <input
            id="log-message"
            v-model="logMessage"
            type="text"
            placeholder="예: User logged in"
            class="input-field"
          />
        </div>
        <button @click="testLoggerAdapter" class="test-btn" :disabled="!logMessage">
          로그 기록
        </button>
      </div>

      <div v-if="loggerResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ loggerResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>C. API Format Adapter - 인터랙티브 데모</h2>
      <p>REST API 응답을 GraphQL 형식으로 변환하는 어댑터입니다.</p>

      <div class="code-block">
        <h3>📝 코드</h3>
        <pre><code>// Target - 클라이언트가 기대하는 데이터 형식
interface GraphQLUserResponse {
  data: {
    user: {
      id: string
      name: string
      email: string
    }
  }
}

// Adaptee - REST API 응답 형식
class RestApiClient {
  fetchUser(userId: string): { user_id: string; full_name: string; email_address: string } {
    return {
      user_id: userId,
      full_name: 'John Doe',
      email_address: 'john@example.com'
    }
  }
}

// Adapter - REST 응답을 GraphQL 형식으로 변환
class ApiFormatAdapter {
  constructor(private restClient: RestApiClient) {}

  getUserData(userId: string): GraphQLUserResponse {
    const restResponse = this.restClient.fetchUser(userId)

    // REST 형식을 GraphQL 형식으로 변환
    return {
      data: {
        user: {
          id: restResponse.user_id,
          name: restResponse.full_name,
          email: restResponse.email_address
        }
      }
    }
  }
}

// 클라이언트는 GraphQL 형식을 기대
function displayUser(adapter: ApiFormatAdapter, userId: string): GraphQLUserResponse {
  return adapter.getUserData(userId)
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>👤 사용자 ID 입력</h3>
        <div class="form-group">
          <label for="user-id">User ID:</label>
          <input
            id="user-id"
            v-model="userId"
            type="text"
            placeholder="예: user-12345"
            class="input-field"
          />
        </div>
        <button @click="testApiAdapter" class="test-btn" :disabled="!userId">
          API 호출 및 변환
        </button>
      </div>

      <div v-if="apiResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ apiResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>D. File Storage Adapter - 인터랙티브 데모</h2>
      <p>서로 다른 클라우드 저장소(AWS S3, Google Cloud)를 통합 인터페이스로 사용합니다.</p>

      <div class="code-block">
        <h3>📝 코드</h3>
        <pre><code>// Target - 통합 파일 저장소 인터페이스
interface FileStorage {
  upload(fileName: string, content: string): string
  download(fileName: string): string
}

// Adaptee 1 - AWS S3 클라이언트
class AwsS3Client {
  putObject(bucket: string, key: string, data: string): string {
    return `AWS S3: ${bucket}/${key}에 데이터 업로드 완료`
  }

  getObject(bucket: string, key: string): string {
    return `AWS S3에서 ${bucket}/${key} 다운로드: [파일 내용]`
  }
}

// Adaptee 2 - Google Cloud Storage
class GoogleCloudStorage {
  uploadFile(path: string, content: string): string {
    return `GCS: ${path}에 파일 업로드 완료`
  }

  downloadFile(path: string): string {
    return `GCS에서 ${path} 다운로드: [파일 내용]`
  }
}

// Adapter 1 - S3 어댑터
class S3StorageAdapter implements FileStorage {
  private bucket = 'my-app-bucket'

  constructor(private s3Client: AwsS3Client) {}

  upload(fileName: string, content: string): string {
    return this.s3Client.putObject(this.bucket, fileName, content)
  }

  download(fileName: string): string {
    return this.s3Client.getObject(this.bucket, fileName)
  }
}

// Adapter 2 - GCS 어댑터
class GcsStorageAdapter implements FileStorage {
  constructor(private gcsClient: GoogleCloudStorage) {}

  upload(fileName: string, content: string): string {
    return this.gcsClient.uploadFile(`uploads/${fileName}`, content)
  }

  download(fileName: string): string {
    return this.gcsClient.downloadFile(`uploads/${fileName}`)
  }
}

// 클라이언트는 통합 인터페이스만 사용 (어느 클라우드든 상관없이)
function storeFile(storage: FileStorage, file: string, data: string): string {
  return storage.upload(file, data)
}</code></pre>
      </div>

      <div class="interactive-demo">
        <h3>☁️ 파일 업로드 테스트</h3>
        <div class="form-group">
          <label for="file-name">파일명:</label>
          <input
            id="file-name"
            v-model="fileName"
            type="text"
            placeholder="예: document.pdf"
            class="input-field"
          />
        </div>

        <div class="storage-selection">
          <label>저장소 선택:</label>
          <button
            @click="selectedStorage = 's3'"
            :class="['storage-btn', { active: selectedStorage === 's3' }]"
          >
            AWS S3
          </button>
          <button
            @click="selectedStorage = 'gcs'"
            :class="['storage-btn', { active: selectedStorage === 'gcs' }]"
          >
            Google Cloud Storage
          </button>
          <button
            @click="selectedStorage = 'both'"
            :class="['storage-btn', { active: selectedStorage === 'both' }]"
          >
            Both (비교)
          </button>
        </div>

        <button @click="testStorageAdapter" class="test-btn" :disabled="!fileName || !selectedStorage">
          파일 업로드/다운로드
        </button>
      </div>

      <div v-if="storageResult" class="result">
        <h3>🚀 실행 결과</h3>
        <pre>{{ storageResult }}</pre>
      </div>
    </div>

    <div class="demo-section">
      <h2>패턴의 장점</h2>
      <div class="benefits-list">
        <h3>✨ 주요 장점</h3>
        <ul>
          <li><strong>OCP 준수:</strong> 기존 코드 수정 없이 새 시스템 통합</li>
          <li><strong>레거시 통합:</strong> 오래된 코드와 최신 라이브러리를 함께 사용</li>
          <li><strong>SRP 준수:</strong> 인터페이스 변환 로직을 별도 클래스로 분리</li>
          <li><strong>재사용성:</strong> 다양한 Adaptee를 같은 Target으로 사용 가능</li>
          <li><strong>유연성:</strong> 런타임에 어댑터 교체 가능</li>
        </ul>
      </div>

      <div class="code-block">
        <h3>📝 실무 활용 사례</h3>
        <pre><code>// 1. 서드파티 라이브러리 통합
class AxiosAdapter implements HttpClient {
  constructor(private axios: AxiosInstance) {}
  // Axios를 자체 HttpClient 인터페이스로 변환
}

// 2. 데이터베이스 드라이버 추상화
class MongoDbAdapter implements DatabaseClient {
  constructor(private mongoClient: MongoClient) {}
  // MongoDB를 통합 DB 인터페이스로 변환
}

// 3. 콜백을 Promise로 변환
class CallbackToPromiseAdapter {
  async execute(callback: Function): Promise&lt;any&gt; {
    return new Promise((resolve, reject) => {
      callback((err, data) => err ? reject(err) : resolve(data))
    })
  }
}

// 4. 외부 API 프로토콜 변환
class SoapToRestAdapter implements RestApi {
  constructor(private soapClient: SoapClient) {}
  // SOAP API를 REST 형식으로 변환
}</code></pre>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const paymentAmount = ref<number | null>(null)
const logMessage = ref<string>('')
const userId = ref<string>('')
const fileName = ref<string>('')
const selectedStorage = ref<'s3' | 'gcs' | 'both' | null>(null)

const paymentResult = ref<string>('')
const loggerResult = ref<string>('')
const apiResult = ref<string>('')
const storageResult = ref<string>('')

// ============ A. Payment Gateway Adapter ============

// Target - 기존 시스템이 기대하는 인터페이스
interface OldPaymentProcessor {
  pay(amount: number): string
}

// Adaptee - 새로운 결제 게이트웨이
class NewPaymentGateway {
  makePayment(value: number, currency: string): string {
    return `New Gateway: ${value} ${currency} 결제 완료`
  }

  getTransactionFee(value: number): number {
    return value * 0.02 // 2% 수수료
  }
}

// Adapter
class PaymentAdapter implements OldPaymentProcessor {
  constructor(private gateway: NewPaymentGateway) {}

  pay(amount: number): string {
    const fee = this.gateway.getTransactionFee(amount)
    const result = this.gateway.makePayment(amount, 'KRW')
    return `${result} (수수료: ${fee}원)`
  }
}

function testPaymentAdapter() {
  if (!paymentAmount.value) return

  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = paymentResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const newGateway = new NewPaymentGateway()
  const adapter = new PaymentAdapter(newGateway)

  const amount = paymentAmount.value
  const fee = newGateway.getTransactionFee(amount)
  const result = adapter.pay(amount)

  paymentResult.value = separator + `[${timestamp}]\n💰 결제 테스트\n\n결제 금액: ${amount.toLocaleString()}원\n\n실행 과정:\n1. PaymentAdapter.pay(${amount}) 호출\n2. NewPaymentGateway.getTransactionFee(${amount}) 호출 → ${fee}원\n3. NewPaymentGateway.makePayment(${amount}, 'KRW') 호출\n\n결과:\n${result}\n\n✅ 기존 인터페이스(pay)를 유지하면서 새 게이트웨이를 사용했습니다!`
}

// ============ B. Logger Adapter ============

interface LegacyLogger {
  log(message: string): string
}

class ModernStructuredLogger {
  logWithMetadata(level: string, message: string, metadata: object): string {
    const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
    return `[${timestamp}] ${level.toUpperCase()}: ${message} | ${JSON.stringify(metadata)}`
  }
}

class LoggerAdapter implements LegacyLogger {
  constructor(private modernLogger: ModernStructuredLogger) {}

  log(message: string): string {
    return this.modernLogger.logWithMetadata(
      'info',
      message,
      { source: 'legacy-system', timestamp: Date.now() }
    )
  }
}

function testLoggerAdapter() {
  if (!logMessage.value) return

  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = loggerResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const modernLogger = new ModernStructuredLogger()
  const adapter = new LoggerAdapter(modernLogger)

  const log = adapter.log(logMessage.value)

  loggerResult.value = separator + `[${timestamp}]\n📝 레거시 로거 통합 테스트\n\n입력 메시지: "${logMessage.value}"\n\n실행 과정:\n1. LegacyLogger.log("${logMessage.value}") 호출\n2. LoggerAdapter가 ModernStructuredLogger로 변환\n3. logWithMetadata(level, message, metadata) 호출\n\n로그 출력:\n${log}\n\n✅ 레거시 코드 수정 없이 최신 로깅 시스템을 사용했습니다!`
}

// ============ C. API Format Adapter ============

interface GraphQLUserResponse {
  data: {
    user: {
      id: string
      name: string
      email: string
    }
  }
}

class RestApiClient {
  fetchUser(userId: string): { user_id: string; full_name: string; email_address: string } {
    return {
      user_id: userId,
      full_name: 'John Doe',
      email_address: 'john@example.com'
    }
  }
}

class ApiFormatAdapter {
  constructor(private restClient: RestApiClient) {}

  getUserData(userId: string): GraphQLUserResponse {
    const restResponse = this.restClient.fetchUser(userId)

    return {
      data: {
        user: {
          id: restResponse.user_id,
          name: restResponse.full_name,
          email: restResponse.email_address
        }
      }
    }
  }
}

function testApiAdapter() {
  if (!userId.value) return

  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = apiResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const restClient = new RestApiClient()
  const adapter = new ApiFormatAdapter(restClient)

  const restData = restClient.fetchUser(userId.value)
  const graphqlData = adapter.getUserData(userId.value)

  apiResult.value = separator + `[${timestamp}]\n🔄 API 형식 변환 테스트\n\n요청 User ID: ${userId.value}\n\nREST API 응답 (Adaptee):\n${JSON.stringify(restData, null, 2)}\n\n⬇️ ApiFormatAdapter 변환 ⬇️\n\nGraphQL 형식 응답 (Target):\n${JSON.stringify(graphqlData, null, 2)}\n\n✅ REST API를 GraphQL 클라이언트에서 사용할 수 있게 되었습니다!`
}

// ============ D. File Storage Adapter ============

interface FileStorage {
  upload(fileName: string, content: string): string
  download(fileName: string): string
}

class AwsS3Client {
  putObject(bucket: string, key: string, data: string): string {
    return `AWS S3: ${bucket}/${key}에 데이터 업로드 완료`
  }

  getObject(bucket: string, key: string): string {
    return `AWS S3에서 ${bucket}/${key} 다운로드: [파일 내용]`
  }
}

class GoogleCloudStorage {
  uploadFile(path: string, content: string): string {
    return `GCS: ${path}에 파일 업로드 완료`
  }

  downloadFile(path: string): string {
    return `GCS에서 ${path} 다운로드: [파일 내용]`
  }
}

class S3StorageAdapter implements FileStorage {
  private bucket = 'my-app-bucket'

  constructor(private s3Client: AwsS3Client) {}

  upload(fileName: string, content: string): string {
    return this.s3Client.putObject(this.bucket, fileName, content)
  }

  download(fileName: string): string {
    return this.s3Client.getObject(this.bucket, fileName)
  }
}

class GcsStorageAdapter implements FileStorage {
  constructor(private gcsClient: GoogleCloudStorage) {}

  upload(fileName: string, content: string): string {
    return this.gcsClient.uploadFile(`uploads/${fileName}`, content)
  }

  download(fileName: string): string {
    return this.gcsClient.downloadFile(`uploads/${fileName}`)
  }
}

function testStorageAdapter() {
  if (!fileName.value || !selectedStorage.value) return

  const timestamp = new Date().toLocaleString('ko-KR', { timeZone: 'Asia/Seoul' })
  const separator = storageResult.value ? '\n\n' + '='.repeat(60) + '\n\n' : ''

  const s3Client = new AwsS3Client()
  const gcsClient = new GoogleCloudStorage()

  const s3Adapter = new S3StorageAdapter(s3Client)
  const gcsAdapter = new GcsStorageAdapter(gcsClient)

  const content = 'File content here...'

  let resultText = ''

  if (selectedStorage.value === 's3') {
    const upload = s3Adapter.upload(fileName.value, content)
    const download = s3Adapter.download(fileName.value)
    resultText = separator + `[${timestamp}]\n☁️ AWS S3 저장소 테스트\n\n파일명: ${fileName.value}\n\n📤 업로드:\n${upload}\n\n📥 다운로드:\n${download}\n\n✅ FileStorage 인터페이스로 S3를 사용했습니다!`
  } else if (selectedStorage.value === 'gcs') {
    const upload = gcsAdapter.upload(fileName.value, content)
    const download = gcsAdapter.download(fileName.value)
    resultText = separator + `[${timestamp}]\n☁️ Google Cloud Storage 테스트\n\n파일명: ${fileName.value}\n\n📤 업로드:\n${upload}\n\n📥 다운로드:\n${download}\n\n✅ FileStorage 인터페이스로 GCS를 사용했습니다!`
  } else {
    // both
    const s3Upload = s3Adapter.upload(fileName.value, content)
    const gcsUpload = gcsAdapter.upload(fileName.value, content)
    const s3Download = s3Adapter.download(fileName.value)
    const gcsDownload = gcsAdapter.download(fileName.value)

    resultText = separator + `[${timestamp}]\n☁️ 다중 클라우드 저장소 비교 테스트\n\n파일명: ${fileName.value}\n\n📤 업로드 테스트:\nS3: ${s3Upload}\nGCS: ${gcsUpload}\n\n📥 다운로드 테스트:\nS3: ${s3Download}\nGCS: ${gcsDownload}\n\n✅ 동일한 FileStorage 인터페이스로 다른 클라우드를 사용했습니다!\n✅ 코드 변경 없이 저장소를 교체할 수 있습니다!`
  }

  storageResult.value = resultText
}
</script>

<style scoped>
/* AdapterPattern 전용 스타일이 필요한 경우 여기에 추가 */
/* 공통 스타일은 src/assets/main.css에서 전역으로 적용됨 */
</style>
