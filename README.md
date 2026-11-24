# FacePass

## English

Use ColorOS face unlock in the system authentication prompt (BiometricPrompt) of the apps you choose.

The ColorOS face sensor reports strength `4095`, which belongs to no Android class, so the system lets no third-party app use face. FacePass lets face pass the eligibility check only for the apps you tick, and only when face suits that particular request. Everything else stays stock.

### Features

- **Whitelist**: face appears only in the apps you tick; every other app keeps stock fingerprint.
- **Per-request judgment**:
  - Requests bound to a Keystore key (with a `CryptoObject`) use fingerprint only. Face is still a weak biometric to the system and cannot unlock such keys.
  - If the same app asks again within a few seconds after face succeeded (common with time-bound keys), that request uses fingerprint only. Turning the screen off clears this record.
- **Older apps**: an app that still verifies through the legacy fingerprint API is carried into the same system prompt, so it gains face alongside fingerprint without being touched itself; if face is not recognised, fingerprint is still there. An app clone borrows the foreground user's face.
- **Prompt panel**: the medium-size ColorOS panel, the same one fingerprint uses. Face and fingerprint start together, and the prompt continues by itself once face succeeds.
- **Early face success**: if your face is already in front of the camera, the result is held until the prompt has finished appearing, so the system does not take it as a cancellation.
- **Fluid Cloud hint**: a hint is shown in Fluid Cloud while face recognition runs.
- **Lock screen**: when the lock screen's own face unlock holds the camera, whitelisted apps can still use face.

### How it works

FacePass injects into no app. Its scope is fixed to System Framework and System UI.

| Where | What |
|---|---|
| system_server | Judges each request (face / fingerprint only / stock) and lets face pass the strength check only for requests judged face; carries a legacy fingerprint request into the prompt; handles the camera held by the lock screen and early face success; posts the Fluid Cloud hint |
| SystemUI | Gives whitelisted apps the medium-size panel, removes the needless confirmation, starts fingerprint together with face |

Face images and matching stay in the hardware and the secure environment (TEE). FacePass sees only success or failure.

### Security

- Face remains a weak biometric to the system: it unlocks no key that requires strong authentication.
- For apps that only check that authentication succeeded, without a key, whether a photo or video can fool face depends on ColorOS face itself. The risk is the same as with face unlock on the lock screen. Leave banking and payment apps unticked unless you need them.

### Requirements

- ColorOS 17 only
- LSPosed with libxposed API 102

### Installation

1. Install the APK, enable FacePass in LSPosed, and make sure the scope includes System Framework and System UI.
2. Reboot.
3. Open FacePass and tick the apps that should use face. Allow notifications the first time the Fluid Cloud hint is used.

Troubleshooting: entries tagged `FacePass` in the LSPosed log show the judgment for each request and, when the system refuses face, the status code.

### License

Copyright © 2026 axiom-fn. All rights reserved. No part of this project may be used, copied, modified or distributed without the author's prior written permission. See [LICENSE](https://github.com/axiom-fn/FacePass/blob/main/LICENSE).

---

## 繁體中文

讓 ColorOS 的人臉辨識可以用在你指定的應用的系統驗證框（BiometricPrompt）。

ColorOS 的人臉感應器上報的強度是 `4095`，不屬於 Android 的任何一個等級，系統因此不讓任何第三方應用使用人臉。FacePass 只在你勾選的應用、而且這次驗證適合用人臉時，讓人臉通過資格檢查；其餘一切維持原生。

### 功能

- **白名單**：只有勾選的應用會出現人臉，其他應用保持原生的指紋。
- **逐次判斷**：
  - 綁定 Keystore 密鑰的驗證（帶 `CryptoObject`）只用指紋。人臉在系統裡仍是弱認證，解不開這類密鑰。
  - 人臉通過後幾秒內同一應用又要求驗證（常見於限時密鑰），那一次只用指紋。關閉螢幕會清除這個紀錄。
- **舊式呼叫**：仍透過舊版指紋介面驗證的應用，會被帶進同一個系統驗證框，因而在不動那個應用的前提下同時獲得人臉與指紋；人臉沒認出時，指紋依然可用。分身應用會借用前台空間的人臉。
- **驗證框**：使用 ColorOS 自帶的中尺寸面板（與指紋驗證相同），人臉與指紋同時啟動，人臉通過後自動繼續。
- **人臉過早通過**：臉已在鏡頭前時，結果會等驗證框顯示完成才交出，不會被系統當成「取消」。
- **流體雲提示**：人臉辨識期間在流體雲顯示「正在尋找你的臉孔」。
- **鎖屏時可用**：鎖屏的人臉解鎖佔用鏡頭時，白名單應用的驗證仍可使用人臉。

### 運作方式

FacePass 不注入任何應用，作用域固定為「系統框架」與「系統介面」。

| 位置 | 做什麼 |
|---|---|
| system_server | 為每個驗證請求作出裁決（人臉／只用指紋／原生），只對裁決為人臉的請求放行人臉的強度檢查；將舊式指紋請求帶進驗證框；處理鏡頭被鎖屏佔用、人臉過早通過，並發出流體雲提示 |
| SystemUI | 讓白名單應用的驗證框使用中尺寸面板、免去多餘的確認、讓指紋與人臉同時啟動 |

人臉的影像與比對都在硬體與安全環境（TEE）中完成，FacePass 只看得到「成功／失敗」。

### 安全說明

- 人臉在系統中仍是弱認證：它不會解開任何需要強認證的密鑰。
- 對只檢查「驗證成功」、不使用密鑰的應用，人臉能否被照片或影片騙過，取決於 ColorOS 人臉本身，風險與鎖屏的人臉解鎖相同。銀行與支付類應用如非必要，建議不要勾選。

### 需求

- 僅支援 ColorOS 17
- 支援 libxposed API 102 的 LSPosed

### 安裝

1. 安裝 APK，在 LSPosed 中啟用 FacePass，確認作用域包含「系統框架」與「系統介面」。
2. 重新啟動。
3. 開啟 FacePass，勾選要使用人臉的應用；第一次使用流體雲提示時允許通知權限。

疑難排解：LSPosed 日誌中標籤為 `FacePass` 的紀錄會列出每次請求的裁決，以及人臉被系統拒絕時的原因代碼。

### 授權

版權所有 © 2026 axiom-fn，保留所有權利。未經作者書面許可，不得使用、複製、修改或散布本專案的任何部分。見 [LICENSE](https://github.com/axiom-fn/FacePass/blob/main/LICENSE)。
