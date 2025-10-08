# NotificationApp – Supabase Realtime 版

雲端同步通知系統：使用 Supabase 資料庫 + Realtime，支援多裝置即時更新。

## 1) 專案啟動
```bash
npm install
cp .env.example .env  # 並填入你的 SUPABASE URL & ANON KEY
npm run dev
```

## 2) Supabase 資料表與安全性
在 Supabase SQL Editor 執行：

```sql
-- 建立通知資料表
create table if not exists public.notifications (
  id bigserial primary key,
  from_user text not null,
  from_name text not null,
  to_user text not null,
  to_name text not null,
  message text not null,
  created_at timestamptz not null default now(),
  read boolean not null default false
);

-- 開啟 RLS
alter table public.notifications enable row level security;

-- DEMO 開放政策（開發/測試用，正式上線請改成嚴格政策）
create policy "Allow anonymous read own"
on public.notifications for select
to anon
using (true);

create policy "Allow anonymous insert"
on public.notifications for insert
to anon
with check (true);

create policy "Allow anonymous update"
on public.notifications for update
to anon
using (true)
with check (true);

create policy "Allow anonymous delete"
on public.notifications for delete
to anon
using (true);
```

> ⚠️ 上述政策為 **範例**，方便快速試跑。上線前請依你的 Auth 規劃改成「僅允許本人存取」的嚴格策略。

## 3) 功能
- 登入（本地模擬三個帳號）
- 發送通知：寫入 Supabase，所有登入該帳號的裝置會即時看到
- 收件匣 / 寄件匣
- 標記已讀、刪除（同步到雲端）

## 4) 環境變數
- `VITE_SUPABASE_URL`
- `VITE_SUPABASE_ANON_KEY`
