# freee 会計API クライアントライブラリ(TypeScript, Axios版)

freee 会計API クライアントライブラリ(TypeScript, Axios版)です。  
***freee公式のライブラリではありません。***

### クライアントライブラリの作成・更新

see. https://developer.freee.co.jp/reference/accounting/reference

一度srcディレクトリを削除・再作成
```shell
rm -rf src
mkdir src
```

using docker
```shell
# https://openapi-generator.tech/docs/generators/#client-generators

docker run --rm \
    -u "$(id -u $USER):$(id -g $USER)" \
    -v "${PWD}:/local" \
    openapitools/openapi-generator-cli:v5.4.0 generate \
    -i https://raw.githubusercontent.com/freee/freee-api-schema/master/v2020_06_15/open-api-3/api-schema.json \
    -g typescript-axios \
    -o /local/src
```

using Node.js (requires Java)
```shell
npm i
npx @openapitools/openapi-generator-cli generate \
    -i https://raw.githubusercontent.com/freee/freee-api-schema/master/v2020_06_15/open-api-3/api-schema.json \
    -g typescript-axios \
    -o /local/src
```

ビルド
```
npm run build
```

### 実装サンプル

```typescript
const companyId = 123456 // 任意の事業所ID
const accessToken = 'some_access_token' // アクセストークン
// あるいは任意の方法によるtoken provider
// const accessToken = async (): Promise<string> => {
// // ファイルやDBからアクセストークンを取得するなどの処理を実装
// return 'ECv7Nct6nn-Lmlpw6Ro4mQcMiu1SgYUtiUQmoAwtBp8'
// }

const configuration = new Configuration({ accessToken });

// 会社取得
const companiesApi = new CompaniesApi(configuration)
const response = await companiesApi.getCompany(companyId)
console.log(response.data)

const receiptsApi = new ReceiptsApi(configuration)

// 証憑アップロードはファイルをBlobで渡す必要がある
const filePath = './upload-sample.pdf';
const buffer = readFileSync(filePath);
const receiptFile = new Blob([buffer]);
const result = await receiptsApi.createReceipt(
  companyId,
  receiptFile,
  'API経由アップロードのサンプル'
  // ...
)
console.log('create receipt result:', result.data)

// 証憑ダウンロードはresponseTypeを明示的に指定しないとバイナリファイルがstringで変えるので下記いずれかを指定する
const receiptId = 123456789
// バイナリで取得する場合
const dlResponse1 = await receiptsApi.downloadReceipt(receiptId, companyId, { responseType: 'arraybuffer' })
// 実際には data は ArrayBufferView になるが、型定義上は string になってしまうので any を経由してキャスト
const data1 = dlResponse1.data as any as NodeJS.ArrayBufferView
writeFileSync('./downloaded-receipt-arraybuffer.pdf', dlResponse1.data)

// streamで取得する場合
const dlResponse2 = await receiptsApi.downloadReceipt(receiptId, companyId, { responseType: 'stream' })
// 実際には data は ReadableStream になるが、型定義上は string になってしまうので any を経由してキャスト
const data2 = dlResponse2.data as any as NodeJS.ReadableStream
data2.pipe(createWriteStream('./downloaded-receipt-stream.pdf'))
```
