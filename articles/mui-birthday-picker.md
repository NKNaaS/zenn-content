---
title: "MUIのDatePickerで誕生日を可能な限り入力しやすくする"
emoji: "🎂"
type: "idea"
topics: [mui, react, tips]
published: true
---

# 概要

誕生日が入力しやすいフォームを作成するのは意外と大変です。一方実装はなるべくサボりたかったので、MUIのDatePickerのオプションを調べていたら、殆ど手を加えることなく実装できてしまいました。なので今回はその知見を共有します。

# 完成形

```tsx
<LocalizationProvider
  dateAdapter={AdapterDateFns}
  adapterLocale={ja}
  localeText={{
    previousMonth: "前月",
      nextMonth: "次月"
  }}
>
  <DatePicker
    label="誕生日"
    mask="____年__月__日"
    inputFormat="yyyy年MM月dd日"
    openTo="year"
    views={["year", "month", "day"]}
    maxDate={new Date()}
    value={value}
    onOpen={() => {
      if (value === null) {
        setValue(new Date(1990, 0, 1));
      }
    }}
    onChange={(newValue) => {
      setValue(newValue);
    }}
    renderInput={(params) => <TextField {...params} />}
  />
</LocalizationProvider>
```

デモ: <https://codesandbox.io/s/birthdaypicker-demo-3sdekh?file=/demo.tsx>

たまに`date-fns/locale/ja`のインポートができないなどと言われますが、一行編集するとなぜか表示できたりします。

# やったこと

## YearPickerを最初に表示する

DatePickerには年を選択するYearPickerが用意されていますが、デフォルト設定では日を選択するCalendarPickerが表示されてしまいます。
これを回避するには、`openTo="year"` を指定して、YearPickerを真っ先に開くよう指定します。

## MonthPickerを表示する

MUIには月を選択するMonthPickerが用意されていますが、デフォルト設定では表示されず、カレンダー表示にある左右の矢印を連打しないといけません。
これを回避するには、`views={["year", "month", "day"]}` のオプションを指定して、MonthPickerも表示するように指定します。これだけで十分実用的です。

## 未来の日付を非表示にする

`maxDate={new Date()}`で非表示にできます。 `disableFuture={true}`は選択できなくなるだけで表示はされます。

## ローカライズする

LocalizationProviderでローカライズできます。LocalizationProviderの`localeText`オプションを指定することで、ariaラベルもローカライズできます。

また、`mask="____年__月__日"`、`inputFormat="yyyy年MM月dd日"`を指定することで、入力結果の表示もローカライズできます。キーボード入力の際も、数字を入力するだけで「年月日」を補完してくれます。

## 最初に表示する年を指定する

例えば1990年付近の生まれの利用者が多い場合、YearPickerを開いたときに1990年が指定されていてほしいです。これは、愚直ではありますが、`onOpen`に初期値を設定する関数を渡すことで実現できます。

```ts
onOpen={() => {
  if (value === null) {
    setValue(new Date(1990, 0, 1));
  }
}}
```

# うまくいかなかったこと

## 今日の日付のハイライトを無効化する

`disableHighlightToday={true}`で今日の日付のハイライトが無効化できるらしいのですが、うまくいきませんでした。というのも、年を選択した段階で、月と日に今日の日付が自動選択されるため、結果として今日の日付がハイライトされるためです。また、年が1900年から表示されるため、なかなかにお年寄り向けのDatePickerになってしまいます。なにかいい方法あれば教えて下さい。

# 参考

MUI DatePicker API Reference: <https://mui.com/x/api/date-pickers/date-picker/>
