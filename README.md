<div align="center">
  <!-- Centered README header hack -->
  <img width="400" src="logo.svg">
  <br><br>
</div>

# iOS版Threema

[這個儲存褲](https://github.com/threema-ch/threema-ios) 包含完整[Threema](https://threema.ch/)iOS版的原始碼。

## 目錄

- [回報Bug/ 功能許願 / 安全性問題](#issues)
- [原始碼發布政策](#release-policy)
- [許可證檢查](#license-checks)
- [框架](#schemes)
- [構建](#building)
- [測試](#testing)
- [可重現構建](#reproducible-builds)
- [程式碼組織/架構](#architecture)
- [貢獻](#contributions)
- [授權條款](#license)


## <a name="issues"></a>回報Bug/ 功能許願 / 安全性問題

想回報bug或者許願新功能, 請聯繫 [Threema 支援團隊](https://threema.ch/support)。

如果發現Threema有安全性問題，請遵守協漏洞回報（CVD）機制。要獲得漏洞賞金，請 [在GObugfree提交報告](https://app.gobugfree.com/programs/threema) (點擊連結查看所列詳細資訊，包括賞金等級)。 如果你對於漏洞賞金不感興趣，可以透過THreema或電郵聯繫我們，關於聯絡資訊詳情，請看 [threema.ch/contact](https://threema.ch/en/contact) (section "Security"）。


## <a name="release-policy"></a>原始碼發布政策

此原始碼儲存庫將針對每個公開的非測試版本進行更新。每個正式版本都會使用 PGP 簽章提交更新。欲知更多資訊，請查看 [SECURITY.md](SECURITY.md) 。


## <a name="license-checks"></a>許可證檢查

雖然iOS版Threema的原始碼是在開源許可下發布，但 Threema 仍是一款付費應用程式，要結合我們的官方伺服器基礎架構執行該應用程式，您必須在 App Store 購買許可證。

Threema有三種不同的許可證檢查方式，具體取決於目標應用程式：

### App Store授權

使用在 [App Store](https://apps.apple.com/gw/app/threema/id578665578) 購買的 Threema 應用程式建立新的 Threema ID 時，會將經數位簽署的 App Store 收據發送到目錄伺服器，伺服器可以驗證您是否確實購買了該應用程式，但無法識別您的身分。

也就是說使用`Threema`框架的自編譯應用程式不能用來建立新的Threema ID。但是，您可以使用在 App Store 購買的應用程式來建立 ID，然後匯出備份，並將此備份導入到自編譯的應用程式中。

請注意，ID 建立端點受到監控，以防止濫用。

### Threema Work

如果您建立了 Threema Work 目標，必須提供在 [Threema Work](https://work.threema.ch/) 訂閱的憑證才能使用該應用程式。
### Threema OnPrem

如果您建立了 Threema Onprem 目標， 必須提供在 [Threema OnPrem](https://threema.ch/en/onprem/) 訂閱的憑證才能使用該應用程式
## <a name="schemes"></a>Schemes

- `Threema` 建立並測試消費者應用程式。 （建議進行本機測試）
- `Threema Work` 建立並測試我們應用程式的企業版。
- `Threema OnPrem` 建立並測試應用程式的地端伺服器版.
- `Threema Green` 僅供Threema內部研發測試使用。
- `Threema Blue` 僅供Threema內部研發測試使用。

## <a name="building"></a>構建

首先，你需要一台安裝[Xcode](https://developer.apple.com/xcode/) (16.1+) 的 [Mac](https://www.apple.com/mac/)，且有一個（免費的） [Apple開發者帳戶](https://developer.apple.com/programs/)。

### 1. 安裝依賴套件

1. 如果你剛安裝 Xcode，請確保已選擇命令列工具。

   ```sh
   sudo xcode-select --switch /Applications/Xcode.app
   ```

2. 安裝並建立依賴套件

   ```sh
   ./scripts/build.sh --dependencies
   ```

   如缺少 `WebRTC.xcframework` 請以這個指令下載。 (如果你想自己建立WebRTC，請看[BUILD_WEBRTC.md](BUILD_WEBRTC.md).)

3. 安裝並初始化 [Rustup](https://rustup.rs) 以建立SaltyRTC。將在專案的第一次編譯期間建置，同時安裝所需的特定工具鏈和目標。
   ```sh
   brew install rustup
   rustup-init
   ```
   (如果你沒有[homebrew](https://brew.sh) 可以查看他們的 [官方安裝指引](https://github.com/Carthage/Carthage#installing-carthage)。)

   (您可能想將 $HOME/.cargo/bin 新增至PATH。)

4. 確保子模組已簽出

   ```sh
   git submodule update --init
   ```

### 2. 設置專案

您可以建置 Threema 應用程式（建議）或 Threema Work 應用程式。
_請留意_:這些設定用於在模擬器中執行。

#### Threema (建議)

1. 在Xcode中打開 `Threema.xcproject` 
2. 對 `Threema` 和 `Threema ShareExtension` 目標重複這些步驟：
   1. 勾選「自動管理簽署」並確認（「啟用自動」）
   2. 將「團隊」設定為你的開發者帳號的團隊
3. 選擇 `Threema` 作為框架與模擬器。

#### Threema Work

1. 在Xcode打開 `Threema.xcproject`
2. 對 `Threema Work` 和 `Threema Work ShareExtension` 目標重複這些步驟：
   1. 勾選「自動管理簽署」並確認（「啟用自動」）
   2. 將「團隊」設定為你的開發者帳號的團隊
3. 選擇 `Threema Work` 作為框架與模擬器。


### 3. 建置與執行

1. 建置與執行
2. 若要建立 Threema ID，請參閱上面的「App Store授權」。 （您可以取消「使用 Apple ID 登入」對話方塊並匯入 Threema ID 備份。）


## <a name="testing"></a>Testing

See "Building" for setting up a running environment. Before running the tests check if you can sucessfully build and run the app.

- Choose `Threema` as scheme to run the app tests.
- Choose `ThreemaFramework` as scheme to run the framework tests.
- Choose `Threema Work` as scheme to run Threema Work specific tests.


## <a name="reproducible-builds"></a>Reproducible Builds

Due to restrictions by Apple, it’s no easy task to offer reproducible builds for iOS, but we are currently evaluating possible ways to also support reproducible builds for this platform.


## <a name="architecture"></a>Code Organization / Architecture

Before digging into the codebase, you should read the [Cryptography Whitepaper](https://threema.ch/press-files/2_documentation/cryptography_whitepaper.pdf) to understand the design concepts.

These are the most important groups of the Xcode project:

- `ThreemaFramework`: Shared code between the main app and extensions
- `Threema`: Code of both apps (Threema and Threema Work)
- `Threema ShareExtension`: Code of share extension
- `Threema{Framework}Tests`: Test files
- `GroupCalls`: Code of group calls

Our dependencies are managed with Swift Package Manager. Additionally we use WebRTC based on binaries hosted on our servers. If you want to build WebRTC yourself see [BUILD_WEBRTC.md](BUILD_WEBRTC.md).


## <a name="contributions"></a>Contributions

We accept GitHub pull requests. Please refer to <https://threema.ch/open-source/contributions> for more information on how to contribute.


## <a name="license"></a>License

Threema for iOS is licensed under the GNU Affero General Public License v3.


    Copyright (c) 2012-2025 Threema GmbH
    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU Affero General Public License, version 3,
    as published by the Free Software Foundation.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
    GNU Affero General Public License for more details.

    You should have received a copy of the GNU Affero General Public License
    along with this program. If not, see <https://www.gnu.org/licenses/>.

The full license text can be found in [`LICENSE.txt`](LICENSE.txt).

If you have questions about the use of self-compiled apps or the license in general, feel free to [contact us](mailto:opensource@threema.ch). We are publishing the source code in good faith, with transparency being the main goal. By having users pay for the development of the app, we can ensure that our goals sustainably align with the goals of our users: Great privacy and security, no ads, no collection of user data!
