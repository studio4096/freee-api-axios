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
