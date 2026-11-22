---
name: code-to-video
description: ساخت ویدیو (ریلز، یوتیوب، تیزر، اینفوگرافیک، متن متحرک فارسی) با کد React و Remotion و رندر به MP4. Use when the user wants to make a video, reel, animation, motion graphic, explainer or title sequence from code, or says «یک ویدیو بساز».
---

# کد → ویدیو با Remotion

هر ویدیو یک کامپوننت React است. Remotion هر فریم را در Chrome می‌کشد و با ffmpeg به MP4 تبدیل می‌کند.

## روال
1. **راه‌اندازی** (یک بار): `bash scripts/setup.sh` (لینوکس/ابر) یا `npm install` (ویندوز/مک).
2. **سناریو:** موضوع، طول، اندازه (عمودی 1080x1920 یا افقی 1920x1080)، و فهرست صحنه‌ها با زمان هر کدام. اگر کاربر عجله ندارد، متن را قبل از کد تأیید بگیر.
3. **کد:** پوشه‌ی `src/vNN/` بساز (NN شماره‌ی ویدیو). هر صحنه یک کامپوننت؛ همه را با `<Sequence from durationInFrames>` پشت هم بچین. در `src/Root.tsx` یک `<Composition>` اضافه کن.
4. **چک:** `npx tsc --noEmit` باید بی‌خطا باشد.
5. **فریم‌های نمونه:** قبل از رندر کامل چند فریم بگیر و خودت نگاه کن:
   `npx remotion still <Id> out/f100.png --frame=100 --scale=0.5`
6. **رندر:**
   - کوتاه: `npx remotion render <Id> out/video.mp4`
   - بلند یا ماشین ضعیف (تکه‌ای، قابل ادامه): `RENDER_BUDGET_SEC=480 node tools/render-chunks.mjs <Id> <frames> out/chunks out/video.mp4 150`
     کد خروج 3 یعنی تمام نشده؛ همان دستور را دوباره بزن.
7. **صدا:** موزیک/افکت را در `public/music` و `public/sfx` بگذار و با `<Audio src={staticFile("music/x.mp3")} volume={0.3} />` اضافه کن. فقط فایل‌های آزاد (CC0، CC BY با ذکر نام) یا فایل خود کاربر.

## قواعد کد
- همه‌ی حرکت‌ها تابع `useCurrentFrame()` باشند (`interpolate`، `spring`). از `setTimeout`، `Math.random()` بی‌seed و انیمیشن CSS استفاده نکن؛ از `random(seed)` خود Remotion استفاده کن.
- فایل‌ها از `public/` با `staticFile()`؛ عکس با `<Img>`، ویدیو با `<OffthreadVideo>`.
- فونت فارسی: `FONT_TEXT` (Vazirmatn) و `FONT_NUM` (Lalezar) از `src/fonts.ts`. متن فارسی: `direction: "rtl"`.
- متن را دور از لبه‌ها نگه دار (حاشیه‌ی امن ~۸٪). در ریلز، پایین ۲۰٪ زیر کپشن اینستاگرام می‌رود.
- صحنه‌ها کوتاه (۲ تا ۵ ثانیه)، ورود و خروج نرم، یک ایده در هر قاب.
- رنگ و پارامترها را به props بده (مثل `demoSchema` در `src/demo/Demo.tsx`) تا عوض کردنشان راحت باشد.

## نمونه
`src/demo/Demo.tsx`: تیتر با spring و سه خط که پشت هم می‌آیند. برای شروع از رویش کپی کن.
مستندات کامل: https://www.remotion.dev/docs
