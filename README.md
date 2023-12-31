# Migrate Metadata
クラウドストレージにあるNFTメタデータを分散ストレージへ移行

## Prerequisites
- [Nodejs](https://nodejs.org/ja)
- [thirdweb API Key](https://portal.thirdweb.com/api-keys)

## マイグレーションを行う。
1. メタ情報とイメージをローカルにダウンロードする
（メタデータ中に定義されているimageのURLからダウンロード）
```json
{
  "description": "Friendly OpenSea Creature that enjoys long swims in the ocean.", 
  "external_url": "https://openseacreatures.io/3", 
  "image": "https://storage.googleapis.com/opensea-prod.appspot.com/puffs/3.png", 
  "name": "Dave Starbelly",
  "attributes": [ ... ]
}
```
2. ダウンロードしたイメージをIPFSストレージにアップロードする
※今回はthirdwebが提供している以下の２つライブラリーを用いて行いました。

- `https://www.npmjs.com/package/thirdweb`
- `go get github.com/thirdweb-dev/go-sdk/v2/thirdweb`

## Help
```shell
./bin/meta-converter -h                                                                                                                                
flag needs an argument: -h
Usage of ./bin/meta-converter:
  -eTokenID int
    	finish to NFT TokenID (default 1)
  -h string
    	Metadata API Base URL (default "https://metadata.baseUrl/xxxxx/")
  -image-outdir string
    	Images output folder name (default "images")
  -meta-outdir string
    	Metadata output folder name (default "metadata")
  -sTokenID int
    	start from NFT TokenID (default 1)

```


## 実行
0. npm install
1. 以下を実行し、メタデータとイメージをダウンロードする
    ```shell
    ./bin/meta-converter -h {メタが取れるAPIベースURL} -sTokenID 開始トークンID -eTokenID 終了トークンID
    ```

    ```shell
    ## 実行ログからipfsのcid確認ができる
    ipfs response : {Image:ipfs://jiorjoiiojgojdfogjdf/0}
    ipfs response : {Image:ipfs://jiorjoiiojgojdfogjdf/0}
    ipfs response : {Image:ipfs://jiorjoiiojgojdfogjdf/0}
    ipfs response : {Image:ipfs://jiorjoiiojgojdfogjdf/0}
    ipfs response : {Image:ipfs://jiorjoiiojgojdfogjdf/0}
    ```
2. 1.を実行するとmetadata / imagesフォルダにそれぞれメタデータjsonファイルと画像がダウンロードされていてる
3. メタデータのimageが既にipfsのURLに書きかわってる
4. 最後メタデータをアップロードする
    ```shell
    ## thirdwebのAPIシークレットキーを要求されるので事前にAPIキーを生成しておく
    npx thirdweb@latest upload metadata
    ```

    ```shell
    ## 正常に実行された例
    ✔ Successfully uploaded directory to IPFS
    ✔ Files stored at the following IPFS URI: ipfs://joewiurefdjasf
    ✔ Open this link to view your upload: https://{client-id}.ipfscdn.io/ipfs/joewiurefdjasf/
    ```
 
 5. 4.取得したURLだとthirdweb側の制限があるのでパブリックでは確認できないため、public gatewayに変更して`https://ipfs.io/ipfs/joewiurefdjasf/`をスマートコントラクトのベーストークンURIを更新

