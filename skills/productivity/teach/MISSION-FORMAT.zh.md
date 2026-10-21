<a id="missionmd-format"></a>

# MISSION.md 格式

`MISSION.md` 位於工作區根目錄。它記錄使用者學習此主題的「原因」。每個教學決策（接下來教什麼、呈現哪些資源、設計哪些練習）都應追溯至這份檔案。

<a id="template"></a>

## 範本

```md
# Mission: {Topic}

## Why
{1-3 sentences. The concrete real-world goal the user is chasing. What changes in their life or work when they have this skill? Avoid abstract framings like "to understand X"; push for the underlying outcome.}

## Success looks like
- {A specific, observable thing the user will be able to do}
- {Another specific thing}
- {…}

## Constraints
- {Time, budget, prior commitments, learning preferences, anything that bounds the approach}

## Out of scope
- {Adjacent topics the user explicitly does not want to chase right now, protecting the zone of proximal development}
```

<a id="rules"></a>

## 規則

- **每個工作區只有一個任務。** 如果使用者想學習兩件不相關的事情，那就需要兩個工作區。
- **具體優於抽象。** 「在十月前跑完半程馬拉松」優於「變得更健康」。「為我的團隊發布一個 Rust CLI」優於「學習 Rust」。
- **對模糊不清的內容提出質疑。** 如果使用者無法說明原因，請在撰寫任何內容前先進行訪談。一個糟糕的任務比沒有任務更糟。
- **當現實改變時進行修訂。** 任務會改變。當使用者的目標發生變化時，請更新此檔案：不要讓過時的任務繼續引導未來的課程。
- **保持簡短。** 如果 `MISSION.md` 超過一螢幕，它就不再是指南針，而開始變成計畫了。
