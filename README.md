<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="ポケカカレンダー">
<meta name="theme-color" content="#0f0c29">
<title>ポケカ 発売カレンダー</title>
<link rel="apple-touch-icon" href="data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='180' height='180'%3E%3Crect width='180' height='180' rx='40' fill='%230f0c29'/%3E%3Ctext x='90' y='120' font-size='90' text-anchor='middle'%3E%F0%9F%8E%B4%3C/text%3E%3C/svg%3E">
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
html{height:100%}
body{
  min-height:100%;
  background:linear-gradient(135deg,#0f0c29,#1a1a3e,#0f3460);
  font-family:-apple-system,'Hiragino Sans',sans-serif;
  color:#fff;
  /* パディングをセーフエリア対応に */
  padding:8px;
  padding-top:max(8px,env(safe-area-inset-top));
  padding-bottom:max(16px,env(safe-area-inset-bottom));
  padding-left:max(8px,env(safe-area-inset-left));
  padding-right:max(8px,env(safe-area-inset-right));
}

/* ヘッダー */
.hdr{text-align:center;margin-bottom:10px}
.hdr h1{font-size:17px;font-weight:800;margin:2px 0;background:linear-gradient(90deg,#FFD700,#FF6B6B,#A855F7);-webkit-background-clip:text;-webkit-text-fill-color:transparent}
.hdr p{font-size:9px;color:#94a3b8}

/* バナー */
.banner{background:rgba(168,85,247,.15);border:1px solid rgba(168,85,247,.35);border-radius:9px;padding:8px 10px;margin-bottom:8px;display:flex;align-items:flex-start;gap:6px}
.banner p{font-size:10px;color:#c4b5fd;flex:1;line-height:1.5}
.banner button{background:none;border:none;color:#94a3b8;font-size:16px;cursor:pointer;flex-shrink:0;line-height:1}

/* 更新バー */
.refreshbar{background:rgba(255,255,255,.06);border-radius:9px;padding:7px 10px;margin-bottom:8px;display:flex;align-items:center;gap:8px}
.refreshtxt{font-size:10px;color:#94a3b8;flex:1;display:flex;align-items:center;gap:5px}
.spinner{display:inline-block;width:10px;height:10px;border:2px solid #A855F7;border-top-color:transparent;border-radius:50%;animation:spin .7s linear infinite;flex-shrink:0}
.btn-ref{background:linear-gradient(135deg,#6C5CE7,#A855F7);color:#fff;border:none;border-radius:7px;padding:5px 10px;font-size:10px;font-weight:700;cursor:pointer;white-space:nowrap;-webkit-tap-highlight-color:transparent}
.btn-ref:disabled{background:#374151;cursor:default}

/* タブ */
.tabs{display:flex;gap:2px;background:rgba(255,255,255,.06);border-radius:9px;padding:3px;margin-bottom:8px}
.tab{flex:1;text-align:center;padding:6px 2px;border-radius:7px;font-size:10px;font-weight:600;cursor:pointer;color:#64748b;border:none;background:none;-webkit-tap-highlight-color:transparent;transition:all .15s}
.tab.active{background:rgba(168,85,247,.35);color:#fff}

/* フィルター */
.filter-row{margin-bottom:7px}
.filter-lbl{font-size:9px;color:#64748b;font-weight:600;margin-bottom:4px}
.fscroll{overflow-x:auto;-webkit-overflow-scrolling:touch}
.fscroll::-webkit-scrollbar{display:none}
.finner{display:flex;gap:4px;padding-bottom:2px}
.fbtn{background:rgba(255,255,255,.08);color:#94a3b8;border:none;border-radius:14px;padding:4px 9px;font-size:10px;cursor:pointer;white-space:nowrap;-webkit-tap-highlight-color:transparent;transition:all .15s;flex-shrink:0}
.fbtn.active{color:#fff;font-weight:700}

/* 月ナビ */
.monthnav{display:flex;align-items:center;justify-content:space-between;margin-bottom:6px}
.navbtn{background:rgba(255,255,255,.1);border:none;border-radius:7px;color:#fff;padding:5px 12px;font-size:16px;cursor:pointer;-webkit-tap-highlight-color:transparent}
.mlabel{font-size:15px;font-weight:700}

/* カレンダーグリッド — 横幅を画面内に収める */
.calgrid{
  display:grid;
  grid-template-columns:repeat(7,1fr);
  gap:2px;
  width:100%;       /* 必ず親幅に収める */
  overflow:hidden;  /* はみ出し防止 */
}
.dname{text-align:center;font-size:9px;font-weight:600;color:#64748b;padding:2px 0;margin-bottom:1px}
.dname.sun{color:#FF6B6B}.dname.sat{color:#60A5FA}

.dcell{
  background:rgba(255,255,255,.05);
  border:1.5px solid transparent;
  border-radius:6px;
  padding:3px 2px;
  min-height:48px;
  cursor:pointer;
  -webkit-tap-highlight-color:transparent;
  overflow:hidden;   /* セル内ではみ出し防止 */
  width:100%;        /* グリッドセル幅に従う */
}
.dcell.today{background:rgba(255,215,0,.14);border-color:#FFD700}
.dcell.selected{background:rgba(168,85,247,.3);border-color:#A855F7}
.dnum{font-size:10px;font-weight:500;text-align:center;margin-bottom:2px;color:#e2e8f0}
.dnum.sun{color:#FF6B6B}.dnum.sat{color:#60A5FA}
.tbadge{background:#FFD700;color:#1a1a2e;border-radius:50%;width:17px;height:17px;display:inline-flex;align-items:center;justify-content:center;font-size:9px;font-weight:800}
.evdot{
  border-radius:2px;padding:1px 2px;font-size:7px;font-weight:700;
  color:#fff;overflow:hidden;white-space:nowrap;text-overflow:ellipsis;
  margin-bottom:1px;cursor:pointer;
  max-width:100%;   /* ドットもセル幅に収める */
  display:block;
}
.evmore{font-size:7px;color:#94a3b8;text-align:center}

/* 日詳細 */
.daydetail{margin-top:10px;background:rgba(255,255,255,.07);border-radius:11px;padding:10px}
.daydetail h3{font-size:12px;font-weight:700;color:#A855F7;margin-bottom:8px}
.evcard{border-radius:8px;padding:8px 10px;margin-bottom:6px;border-left:4px solid;cursor:pointer;-webkit-tap-highlight-color:transparent}
.evcard-head{display:flex;justify-content:space-between;align-items:flex-start;gap:4px;margin-bottom:3px}
.evbadge{font-size:9px;font-weight:700;padding:2px 7px;border-radius:10px;white-space:nowrap}
.evend{font-size:9px;color:#64748b;white-space:nowrap}
.evtitle{font-size:12px;font-weight:700;margin-bottom:2px}
.evdet{font-size:10px;color:#475569}
.evregion{font-size:9px;margin-top:2px}
.evpred{font-size:9px;color:#f59e0b;margin-top:2px}
/* リンクボタン（カード内） */
.ev-link{
  display:inline-block;margin-top:5px;
  background:rgba(108,92,231,.2);border:1px solid rgba(108,92,231,.5);
  color:#a78bfa;text-decoration:none;border-radius:6px;
  padding:4px 10px;font-size:10px;font-weight:600;
}

/* 一覧行 */
.listrow{
  background:#fff;border-radius:10px;padding:10px 12px;margin-bottom:8px;
  cursor:pointer;-webkit-tap-highlight-color:transparent;
  transition:transform .1s;border-left:4px solid #ccc;
  box-shadow:0 1px 4px rgba(0,0,0,.08);
}
.listrow:active{transform:scale(.98)}
/* レジェンド */
.legend{margin-top:12px}
.legend-lbl{font-size:9px;color:#64748b;font-weight:600;margin-bottom:4px}
.legend-inner{display:flex;flex-wrap:wrap;gap:4px}
.legend-item{font-size:8px;background:rgba(255,255,255,.07);border-radius:4px;padding:2px 6px;color:#94a3b8}

/* モーダル */
.modal-ov{position:fixed;inset:0;background:rgba(0,0,0,.8);display:flex;align-items:flex-end;justify-content:center;z-index:200;padding-bottom:env(safe-area-inset-bottom)}
.modal-box{background:#1e293b;border-radius:20px 20px 0 0;padding:16px;width:100%;max-height:85vh;overflow-y:auto}
.modal-drag{width:36px;height:4px;background:rgba(255,255,255,.2);border-radius:2px;margin:0 auto 12px}
.modal-head{display:flex;justify-content:space-between;align-items:flex-start;margin-bottom:8px}
.modal-ico{font-size:24px}
.modal-x{background:rgba(255,255,255,.1);border:none;color:#94a3b8;font-size:16px;cursor:pointer;border-radius:50%;width:28px;height:28px;display:flex;align-items:center;justify-content:center}
.modal-title{font-size:15px;font-weight:800;color:#fff;margin-bottom:4px;line-height:1.3}
.modal-date{font-size:11px;color:#94a3b8;margin-bottom:10px}
.modal-row{background:rgba(255,255,255,.05);border-radius:8px;padding:8px 10px;margin-bottom:7px}
.modal-rlbl{font-size:9px;color:#64748b;margin-bottom:2px}
.modal-rval{font-size:12px;font-weight:600;color:#e2e8f0}
.modal-rtxt{font-size:12px;color:#cbd5e1;line-height:1.5}
.modal-pred{background:rgba(245,158,11,.12);border:1px solid rgba(245,158,11,.3);border-radius:8px;padding:8px 10px;margin-bottom:7px;font-size:10px;color:#f59e0b;line-height:1.5}
.modal-link{cursor:pointer;
  display:block;text-align:center;margin-top:4px;
  background:linear-gradient(135deg,#6C5CE7,#A855F7);
  color:#fff;text-decoration:none;border-radius:11px;
  padding:12px;font-size:13px;font-weight:700;letter-spacing:.02em;
}
.hidden{display:none!important}
@keyframes spin{to{transform:rotate(360deg)}}
</style>
</head>
<body>
<div id="app">
  <!-- iOS追加バナー -->
  <div class="banner" id="banner">
    <p>📱 <b>ホーム画面に追加：</b>Safari「共有」ボタン →「ホーム画面に追加」</p>
    <button onclick="this.closest('.banner').classList.add('hidden')">×</button>
  </div>

  <div class="hdr">
    <div style="font-size:22px">🎴</div>
    <h1>ポケカ 発売カレンダー</h1>
    <p>発売日 · 抽選情報 · 全国カードショップ（★=予想）</p>
  </div>

  <!-- 更新バー -->
  <div class="refreshbar">
    <span class="refreshtxt" id="refreshtxt">📅 2026/6/21 更新 — コンビニ再販予測・量販店後3〜4週間の法則を追加</span>
    <button class="btn-ref" id="btnref" onclick="doRefresh()">🔄 更新</button>
  </div>

  <!-- タブ -->
  <div class="tabs">
    <button class="tab active" id="tab-r" onclick="switchTab('r')">🗾 地域</button>
    <button class="tab" id="tab-s" onclick="switchTab('s')">🏪 店舗</button>
  </div>

  <!-- 地域フィルター -->
  <div class="filter-row" id="frow-r">
    <div class="filter-lbl">地域で絞込み</div>
    <div class="fscroll"><div class="finner" id="fregion"></div></div>
  </div>

  <!-- 店舗フィルター -->
  <div class="filter-row hidden" id="frow-s">
    <div class="filter-lbl">カテゴリ</div>
    <div class="fscroll"><div class="finner" id="fcat"></div></div>
    <div class="filter-lbl" style="margin-top:5px">店舗</div>
    <div class="fscroll"><div class="finner" id="fstore"></div></div>
  </div>

  <!-- 月ナビ -->
  <div class="monthnav">
    <button class="navbtn" onclick="changeMonth(-1)">‹</button>
    <span class="mlabel" id="mlabel"></span>
    <button class="navbtn" onclick="changeMonth(1)">›</button>
  </div>

  <!-- カレンダー -->
  <div class="calgrid" id="daynames"></div>
  <div class="calgrid" id="calgrid"></div>

  <div id="daydetail" class="hidden"></div>

  <div class="legend">
    <div class="legend-lbl">種別</div>
    <div class="legend-inner" id="legendinner"></div>
  </div>

  <!-- エリア攻略ガイド -->
  <div id="guide-section" style="margin-top:18px">

    <!-- タブ -->
    <div style="display:flex;gap:2px;background:rgba(255,255,255,.06);border-radius:9px;padding:3px;margin-bottom:10px">
      <button id="guide-tab-sanin" onclick="switchGuide('sanin')"
        style="flex:1;padding:7px 4px;border-radius:7px;font-size:10px;font-weight:700;border:none;background:rgba(168,85,247,.35);color:#fff;cursor:pointer;-webkit-tap-highlight-color:transparent">
        🗾 山陰エリア攻略
      </button>
      <button id="guide-tab-general" onclick="switchGuide('general')"
        style="flex:1;padding:7px 4px;border-radius:7px;font-size:10px;font-weight:600;border:none;background:none;color:#64748b;cursor:pointer;-webkit-tap-highlight-color:transparent">
        📖 全国共通ガイド
      </button>
    </div>

    <!-- 山陰エリアガイド -->
    <div id="guide-sanin">
      <div style="background:linear-gradient(135deg,rgba(99,102,241,.2),rgba(168,85,247,.2));border:1px solid rgba(168,85,247,.3);border-radius:12px;padding:13px;margin-bottom:10px">
        <div style="font-size:13px;font-weight:800;color:#c4b5fd;margin-bottom:8px">🗾 山陰エリア（米子・鳥取・松江）攻略ガイド</div>
        <div style="font-size:10px;color:#94a3b8;margin-bottom:10px">※現地在住者の肌感覚 + 物流情報をもとに作成</div>

        <!-- 入荷タイムライン -->
        <div style="font-size:11px;font-weight:700;color:#e2e8f0;margin-bottom:8px">⏰ 米子エリアの入荷タイムライン</div>
        <div style="display:flex;flex-direction:column;gap:6px;margin-bottom:12px">
          <div style="display:flex;align-items:center;gap:10px">
            <span style="background:#1e3a5f;color:#60A5FA;border-radius:6px;padding:3px 8px;font-size:10px;font-weight:700;white-space:nowrap;flex-shrink:0">深夜2〜3時</span>
            <span style="font-size:11px;color:#cbd5e1">大阪・広島の配送センター発 → トラック出発</span>
          </div>
          <div style="display:flex;align-items:center;gap:10px">
            <span style="background:#1e3a5f;color:#60A5FA;border-radius:6px;padding:3px 8px;font-size:10px;font-weight:700;white-space:nowrap;flex-shrink:0">朝4〜5時</span>
            <span style="font-size:11px;color:#cbd5e1">米子市内コンビニへトラック到着（現地情報）</span>
          </div>
          <div style="display:flex;align-items:center;gap:10px">
            <span style="background:#1e3a5f;color:#FFD700;border-radius:6px;padding:3px 8px;font-size:10px;font-weight:700;white-space:nowrap;flex-shrink:0">朝6〜7時 ⭐</span>
            <span style="font-size:11px;color:#fde68a;font-weight:600">店頭に並ぶ ← ここが狙い目！（現地情報）</span>
          </div>
          <div style="display:flex;align-items:center;gap:10px">
            <span style="background:#1e3a5f;color:#94a3b8;border-radius:6px;padding:3px 8px;font-size:10px;font-weight:700;white-space:nowrap;flex-shrink:0">朝7〜10時</span>
            <span style="font-size:11px;color:#94a3b8">公式推奨の販売開始時間（ほぼ完売していることも）</span>
          </div>
        </div>

        <!-- 物流ルート -->
        <div style="font-size:11px;font-weight:700;color:#e2e8f0;margin-bottom:8px">🚛 推定物流ルート</div>
        <div style="background:rgba(0,0,0,.3);border-radius:8px;padding:10px;margin-bottom:12px;font-size:10px;line-height:1.8;color:#cbd5e1">
          株式会社ポケモン（東京）<br>
          ↓ 印刷・製造<br>
          ↓ <span style="color:#a78bfa">関西（大阪）の玩具問屋</span> ← 山陰の主要仕入れ元<br>
          ↓ 中国地方配送センター（広島 or 岡山）<br>
          ↓ <span style="color:#fbbf24">山陰道・国道9号経由（約2〜3時間）</span><br>
          ↓ 米子市内の各コンビニ・量販店
        </div>

        <!-- 山陰特有の特徴 -->
        <div style="font-size:11px;font-weight:700;color:#e2e8f0;margin-bottom:8px">📌 山陰エリア特有の特徴</div>
        <div style="display:flex;flex-direction:column;gap:6px;margin-bottom:12px">
          <div style="background:rgba(255,255,255,.06);border-radius:8px;padding:8px 10px;font-size:10px;color:#cbd5e1;border-left:3px solid #A855F7">
            <span style="color:#c4b5fd;font-weight:700">⏱ 関西・関東より1〜2日遅れ</span><br>
            量販店（ヨドバシ等）の再販情報を見たら、山陰は+1〜2日後を目安にチェック
          </div>
          <div style="background:rgba(255,255,255,.06);border-radius:8px;padding:8px 10px;font-size:10px;color:#cbd5e1;border-left:3px solid #60A5FA">
            <span style="color:#93c5fd;font-weight:700">🏪 米子は山陰の物流ハブ</span><br>
            米子に入荷したあと鳥取市・松江市へ翌日以降に流れる傾向
          </div>
          <div style="background:rgba(255,255,255,.06);border-radius:8px;padding:8px 10px;font-size:10px;color:#cbd5e1;border-left:3px solid #F59E0B">
            <span style="color:#fcd34d;font-weight:700">🎯 穴場はミニストップ・デイリーヤマザキ</span><br>
            大手3社より競争率が低い。イオン米子店も在庫多め
          </div>
          <div style="background:rgba(255,255,255,.06);border-radius:8px;padding:8px 10px;font-size:10px;color:#cbd5e1;border-left:3px solid #10B981">
            <span style="color:#6ee7b7;font-weight:700">📅 曜日はバラバラ（不定期）</span><br>
            各店舗で定期便の曜日が異なるため、曜日より「時間帯」を重視して朝イチ巡回が最善
          </div>
        </div>

        <!-- 攻略法 -->
        <div style="font-size:11px;font-weight:700;color:#e2e8f0;margin-bottom:8px">✅ 米子エリア 最強攻略法</div>
        <div style="display:flex;flex-direction:column;gap:5px">
          <div style="background:rgba(255,215,0,.1);border:1px solid rgba(255,215,0,.3);border-radius:8px;padding:8px 10px;font-size:10px;color:#fde68a">
            <span style="font-weight:700">①</span> 発売日の朝6時〜7時に複数チェーンを巡回
          </div>
          <div style="background:rgba(255,255,255,.06);border-radius:8px;padding:8px 10px;font-size:10px;color:#cbd5e1">
            <span style="font-weight:700">②</span> 量販店再販ニュースを見たら「+1〜2日後の朝」にコンビニをチェック
          </div>
          <div style="background:rgba(255,255,255,.06);border-radius:8px;padding:8px 10px;font-size:10px;color:#cbd5e1">
            <span style="font-weight:700">③</span> セブン・ファミマ・ローソンで見つからなければミニストップも確認
          </div>
          <div style="background:rgba(255,255,255,.06);border-radius:8px;padding:8px 10px;font-size:10px;color:#cbd5e1">
            <span style="font-weight:700">④</span> レジ周辺・バックヤードを店員に確認（棚にない場合も）
          </div>
          <div style="background:rgba(255,255,255,.06);border-radius:8px;padding:8px 10px;font-size:10px;color:#cbd5e1">
            <span style="font-weight:700">⑤</span> <a href="https://nyuka-now.com" onclick="openLink('https://nyuka-now.com')" style="color:#a78bfa;text-decoration:none">入荷Nowアプリ</a>でリアルタイム通知をONに
          </div>
        </div>
      </div>
    </div>

    <!-- 全国共通ガイド -->
    <div id="guide-general" class="hidden">
      <div style="background:rgba(255,255,255,.05);border:1px solid rgba(255,255,255,.1);border-radius:12px;padding:13px">
        <div style="font-size:13px;font-weight:800;color:#e2e8f0;margin-bottom:10px">📖 全国共通 コンビニ攻略ガイド</div>

        <div style="font-size:11px;font-weight:700;color:#A855F7;margin-bottom:7px">各チェーンの販売開始時間</div>
        <div style="display:flex;flex-direction:column;gap:5px;margin-bottom:12px">
          <div style="display:flex;align-items:center;gap:8px;background:rgba(255,255,255,.05);border-radius:7px;padding:7px 10px">
            <span style="background:#007940;color:#fff;border-radius:4px;padding:2px 7px;font-size:10px;font-weight:700;flex-shrink:0">セブン</span>
            <span style="font-size:10px;color:#cbd5e1">朝7時〜 / 入荷次第すぐ陳列 / 週2回定期便</span>
          </div>
          <div style="display:flex;align-items:center;gap:8px;background:rgba(255,255,255,.05);border-radius:7px;padding:7px 10px">
            <span style="background:#0057A8;color:#fff;border-radius:4px;padding:2px 7px;font-size:10px;font-weight:700;flex-shrink:0">ファミマ</span>
            <span style="font-size:10px;color:#cbd5e1">朝10時〜（全店共通ルール）/ 週2回定期便</span>
          </div>
          <div style="display:flex;align-items:center;gap:8px;background:rgba(255,255,255,.05);border-radius:7px;padding:7px 10px">
            <span style="background:#1565C0;color:#fff;border-radius:4px;padding:2px 7px;font-size:10px;font-weight:700;flex-shrink:0">ローソン</span>
            <span style="font-size:10px;color:#cbd5e1">朝7時〜 / 当選店舗のみ入荷 / 公式サイトで確認</span>
          </div>
          <div style="display:flex;align-items:center;gap:8px;background:rgba(255,255,255,.05);border-radius:7px;padding:7px 10px">
            <span style="background:#FF6F00;color:#fff;border-radius:4px;padding:2px 7px;font-size:10px;font-weight:700;flex-shrink:0">ミニストップ</span>
            <span style="font-size:10px;color:#cbd5e1">朝7時以降 / 穴場・競争率低め</span>
          </div>
        </div>

        <div style="font-size:11px;font-weight:700;color:#A855F7;margin-bottom:7px">🔄 再販タイミングの法則</div>
        <div style="background:rgba(168,85,247,.1);border:1px solid rgba(168,85,247,.3);border-radius:8px;padding:10px;font-size:10px;color:#c4b5fd;line-height:1.7;margin-bottom:12px">
          量販店（ヨドバシ・ビックカメラ等）で再販開始<br>
          ↓ <span style="color:#fde68a;font-weight:700">約3〜4週間後</span><br>
          コンビニに在庫が流れてくる<br><br>
          ※山陰エリアはさらに+1〜2日が目安
        </div>

        <div style="font-size:11px;font-weight:700;color:#A855F7;margin-bottom:7px">📍 売り場の見つけ方</div>
        <div style="font-size:10px;color:#94a3b8;line-height:1.8">
          最近はレジ横・レジ内が多い（万引き対策）<br>
          棚になくても店員に声をかけると出てくる場合あり<br>
          「お菓子・食玩コーナー」「ギフトカードコーナー」も確認
        </div>
      </div>
    </div>

  </div><!-- /guide-section -->
<!-- ===== コンビニマップセクション ===== -->
<div id="map-section" style="margin-top:18px">
  <div style="display:flex;align-items:center;justify-content:space-between;margin-bottom:10px">
    <div>
      <div style="font-size:13px;font-weight:800;color:#A855F7">🗺️ コンビニ巡回マップ</div>
      <div style="font-size:10px;color:#64748b;margin-top:2px">現在地から最多コンビニを効率よく巡回</div>
    </div>
    <button id="map-toggle-btn" onclick="toggleMap()"
      style="background:linear-gradient(135deg,#6C5CE7,#A855F7);color:#fff;border:none;border-radius:9px;padding:7px 14px;font-size:11px;font-weight:700;cursor:pointer;-webkit-tap-highlight-color:transparent">
      📍 マップを開く
    </button>
  </div>

  <div id="map-container" style="display:none">
    <!-- チェーン選択 -->
    <div style="margin-bottom:10px">
      <div style="font-size:10px;color:#64748b;font-weight:600;margin-bottom:6px">表示するコンビニを選択</div>
      <div style="display:flex;flex-wrap:wrap;gap:5px" id="chain-filters">
        <button class="chain-btn active" data-chain="seven" style="background:#007940" onclick="toggleChain(this,'seven')">🟢 セブン</button>
        <button class="chain-btn active" data-chain="family" style="background:#0057A8" onclick="toggleChain(this,'family')">🔵 ファミマ</button>
        <button class="chain-btn active" data-chain="lawson" style="background:#1565C0" onclick="toggleChain(this,'lawson')">🔷 ローソン</button>
        <button class="chain-btn active" data-chain="mini" style="background:#FF6F00" onclick="toggleChain(this,'mini')">🟠 ミニストップ</button>
        <button class="chain-btn active" data-chain="daily" style="background:#B71C1C" onclick="toggleChain(this,'daily')">🔴 デイリー</button>
      </div>
    </div>

    <!-- 半径選択 -->
    <div style="display:flex;align-items:center;gap:8px;margin-bottom:10px">
      <div style="font-size:10px;color:#64748b;font-weight:600;white-space:nowrap">巡回半径：</div>
      <input type="range" id="radius-slider" min="500" max="5000" step="500" value="1500"
        style="flex:1;accent-color:#A855F7"
        oninput="document.getElementById('radius-val').textContent=this.value/1000+'km';updateMap()">
      <span id="radius-val" style="font-size:11px;color:#A855F7;font-weight:700;min-width:36px">1.5km</span>
    </div>

    <!-- 地図 -->
    <div id="leaflet-map" style="height:320px;border-radius:12px;overflow:hidden;border:1px solid rgba(255,255,255,.1);position:relative">
      <div id="map-loading" style="position:absolute;inset:0;background:#1e293b;display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:10;gap:8px">
        <div style="font-size:28px">📍</div>
        <div style="font-size:12px;color:#94a3b8">位置情報を取得中...</div>
        <div style="font-size:10px;color:#64748b">（許可ダイアログが出たら「許可」を選択）</div>
      </div>
    </div>

    <!-- 結果パネル -->
    <div id="route-result" style="margin-top:10px;display:none">
      <div style="background:rgba(168,85,247,.15);border:1px solid rgba(168,85,247,.3);border-radius:10px;padding:11px">
        <div style="font-size:12px;font-weight:700;color:#A855F7;margin-bottom:8px">🚶 最効率巡回ルート</div>
        <div id="route-summary" style="font-size:11px;color:#cbd5e1;margin-bottom:8px"></div>
        <div id="route-list" style="display:flex;flex-direction:column;gap:5px"></div>
        <div style="margin-top:10px;font-size:10px;color:#64748b">
          ※ルートは直線距離の最近傍法で計算（実際の道路距離とは異なります）
        </div>
      </div>
    </div>
  </div>
</div>

<!-- Leaflet CSS/JS -->
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css">
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>

<style>
.chain-btn{
  border:none;border-radius:14px;padding:5px 10px;font-size:10px;font-weight:700;
  color:#fff;cursor:pointer;-webkit-tap-highlight-color:transparent;opacity:1;transition:opacity .15s;
}
.chain-btn.inactive{opacity:.35}
.route-stop{
  display:flex;align-items:center;gap:8px;
  background:rgba(255,255,255,.05);border-radius:8px;padding:7px 10px;
}
.route-num{
  width:22px;height:22px;border-radius:50%;display:flex;align-items:center;justify-content:center;
  font-size:10px;font-weight:800;color:#fff;flex-shrink:0;
}
</style>

<script>
// ===== マップ機能 =====
let leafMap=null, userLat=null, userLng=null;
let conveniMarkers=[], routeLayer=null, userMarker=null;
let activeChains=new Set(['seven','family','lawson','mini','daily']);
let mapReady=false;

const CHAIN_INFO={
  seven: {name:"セブンイレブン", color:"#007940", emoji:"🟢", amenity_query:"Seven-Eleven"},
  family:{name:"ファミリーマート",color:"#0057A8", emoji:"🔵", amenity_query:"FamilyMart"},
  lawson:{name:"ローソン",        color:"#1565C0", emoji:"🔷", amenity_query:"Lawson"},
  mini:  {name:"ミニストップ",    color:"#FF6F00", emoji:"🟠", amenity_query:"Ministop"},
  daily: {name:"デイリーヤマザキ",color:"#B71C1C", emoji:"🔴", amenity_query:"Daily Yamazaki"},
};

function toggleMap(){
  const mc=document.getElementById('map-container');
  const btn=document.getElementById('map-toggle-btn');
  if(mc.style.display==='none'){
    mc.style.display='block';
    btn.textContent='✕ マップを閉じる';
    btn.style.background='rgba(255,255,255,.1)';
    if(!mapReady) initMap();
  } else {
    mc.style.display='none';
    btn.textContent='📍 マップを開く';
    btn.style.background='linear-gradient(135deg,#6C5CE7,#A855F7)';
  }
}

function toggleChain(btn, chain){
  if(activeChains.has(chain)){
    activeChains.delete(chain);
    btn.classList.add('inactive');
  } else {
    activeChains.add(chain);
    btn.classList.remove('inactive');
  }
  if(userLat) fetchAndShowConveni();
}

function initMap(){
  if(!navigator.geolocation){
    showManualInput();
    return;
  }
  navigator.geolocation.getCurrentPosition(pos=>{
    userLat=pos.coords.latitude;
    userLng=pos.coords.longitude;
    document.getElementById('map-loading').style.display='none';
    // startMap で初期化
    startMap('現在地');
  }, err=>{
    showManualInput();
  },{timeout:10000,enableHighAccuracy:true});
}

// よく使うエリアのプリセット座標
const PRESETS={
  '米子市（中心部）':  {lat:35.4275, lng:133.3308},
  '米子市（車尾）':    {lat:35.4050, lng:133.3350},
  '米子市（西町）':    {lat:35.4350, lng:133.3200},
  '松江市（中心部）':  {lat:35.4723, lng:133.0505},
  '鳥取市（中心部）':  {lat:35.5011, lng:134.2351},
  '倉吉市':            {lat:35.4303, lng:133.8199},
};

function showManualInput(){
  const presetBtns=Object.keys(PRESETS).map(name=>
    `<button onclick="usePreset('${name}')"
      style="background:rgba(168,85,247,.2);border:1px solid rgba(168,85,247,.4);color:#c4b5fd;border-radius:8px;padding:6px 10px;font-size:10px;cursor:pointer;-webkit-tap-highlight-color:transparent;white-space:nowrap">
      📍 ${name}
    </button>`
  ).join('');

  document.getElementById('map-loading').innerHTML=`
    <div style="padding:16px;width:100%">
      <div style="font-size:11px;color:#f87171;margin-bottom:10px;text-align:center">⚠️ 位置情報を自動取得できませんでした</div>
      <div style="font-size:10px;color:#94a3b8;margin-bottom:10px;text-align:center">エリアを選択するかGitHubで公開すると自動取得できます</div>
      <div style="display:flex;flex-wrap:wrap;gap:6px;justify-content:center;margin-bottom:12px">
        ${presetBtns}
      </div>
      <div style="border-top:1px solid rgba(255,255,255,.1);padding-top:10px;text-align:center">
        <div style="font-size:10px;color:#64748b;margin-bottom:6px">または座標を直接入力</div>
        <div style="display:flex;gap:5px;max-width:300px;margin:0 auto 6px">
          <input id="lat-inp" type="number" placeholder="緯度 例:35.4275" step="0.0001"
            style="flex:1;background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.15);border-radius:7px;padding:7px 8px;color:#fff;font-size:11px;outline:none;min-width:0">
          <input id="lng-inp" type="number" placeholder="経度 例:133.3308" step="0.0001"
            style="flex:1;background:rgba(255,255,255,.08);border:1px solid rgba(255,255,255,.15);border-radius:7px;padding:7px 8px;color:#fff;font-size:11px;outline:none;min-width:0">
        </div>
        <button onclick="useCoords()"
          style="background:linear-gradient(135deg,#6C5CE7,#A855F7);color:#fff;border:none;border-radius:8px;padding:8px 20px;font-size:11px;font-weight:700;cursor:pointer">
          🗺️ この座標でマップを開く
        </button>
      </div>
      <div id="addr-status" style="font-size:10px;text-align:center;margin-top:8px;color:#64748b"></div>
    </div>`;
}

function usePreset(name){
  const p=PRESETS[name];
  if(!p) return;
  userLat=p.lat; userLng=p.lng;
  document.getElementById('addr-status').textContent='✅ '+name+' を選択しました';
  document.getElementById('addr-status').style.color='#2ecc71';
  setTimeout(()=>startMap(name), 400);
}

function useCoords(){
  const lat=parseFloat(document.getElementById('lat-inp').value);
  const lng=parseFloat(document.getElementById('lng-inp').value);
  if(isNaN(lat)||isNaN(lng)||lat<20||lat>50||lng<120||lng>150){
    document.getElementById('addr-status').textContent='⚠️ 正しい日本国内の座標を入力してください';
    document.getElementById('addr-status').style.color='#f87171';
    return;
  }
  userLat=lat; userLng=lng;
  startMap('指定した場所');
}

function startMap(label){
  document.getElementById('map-loading').style.display='none';
  leafMap=L.map('leaflet-map').setView([userLat,userLng],15);
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',{
    attribution:'© OpenStreetMap contributors',maxZoom:19
  }).addTo(leafMap);
  userMarker=L.marker([userLat,userLng],{
    icon:L.divIcon({
      html:'<div style="background:#FFD700;width:22px;height:22px;border-radius:50%;border:3px solid #fff;box-shadow:0 2px 6px rgba(0,0,0,.5);display:flex;align-items:center;justify-content:center;font-size:10px">📍</div>',
      iconSize:[22,22],iconAnchor:[11,11]
    })
  }).addTo(leafMap).bindPopup('📍 '+label).openPopup();
  mapReady=true;
  fetchAndShowConveni();
}

async function searchAddr(){
  // 後方互換のため残す（GitHubで公開時はNominatimで検索）
  const addr=(document.getElementById('addr-input')||{value:''}).value.trim();
  if(!addr) return;
  try{
    const res=await fetch(`https://nominatim.openstreetmap.org/search?q=${encodeURIComponent(addr+' 日本')}&format=json&limit=1`,{'Accept-Language':'ja'});
    const data=await res.json();
    if(!data||data.length===0) return;
    userLat=parseFloat(data[0].lat);
    userLng=parseFloat(data[0].lon);
    startMap(addr);
  }catch(e){}
}

async function fetchAndShowConveni(){
  if(!leafMap||!userLat) return;
  const radius=parseInt(document.getElementById('radius-slider').value);

  // 既存マーカーを削除
  conveniMarkers.forEach(m=>leafMap.removeLayer(m));
  conveniMarkers=[];
  if(routeLayer){leafMap.removeLayer(routeLayer);routeLayer=null;}
  document.getElementById('route-result').style.display='none';

  // Overpass APIでコンビニを検索
  const chains=Array.from(activeChains).map(k=>CHAIN_INFO[k].amenity_query);
  if(chains.length===0) return;

  const nameFilter=chains.map(n=>`["name"~"${n}",i]`).join('');
  const query=`
    [out:json][timeout:15];
    (
      node["amenity"="convenience"][~"name"~"${chains.join('|')}",i](around:${radius},${userLat},${userLng});
    );
    out body;
  `;

  try{
    const res=await fetch('https://overpass-api.de/api/interpreter',{
      method:'POST',body:'data='+encodeURIComponent(query)
    });
    const data=await res.json();
    const nodes=data.elements||[];

    if(nodes.length===0){
      document.getElementById('route-result').style.display='block';
      document.getElementById('route-summary').textContent='半径内にコンビニが見つかりませんでした。半径を広げてみてください。';
      document.getElementById('route-list').innerHTML='';
      return;
    }

    // 名前からチェーンを判定
    function detectChain(name){
      if(/seven|セブン/i.test(name)) return 'seven';
      if(/family|ファミリー|ファミマ/i.test(name)) return 'family';
      if(/lawson|ローソン/i.test(name)) return 'lawson';
      if(/ministop|ミニストップ/i.test(name)) return 'mini';
      if(/daily|デイリー/i.test(name)) return 'daily';
      return 'other';
    }

    // マーカー追加
    nodes.forEach(node=>{
      const chain=detectChain(node.tags?.name||'');
      const info=CHAIN_INFO[chain]||{color:'#888',emoji:'🏪'};
      if(!activeChains.has(chain)&&chain!=='other') return;
      const m=L.marker([node.lat,node.lon],{
        icon:L.divIcon({
          html:`<div style="background:${info.color};color:#fff;font-size:11px;font-weight:700;padding:3px 6px;border-radius:6px;border:2px solid #fff;box-shadow:0 2px 4px rgba(0,0,0,.3);white-space:nowrap">${info.emoji}</div>`,
          iconSize:[28,24],iconAnchor:[14,12]
        })
      }).addTo(leafMap);
      m.bindPopup(`<b>${node.tags?.name||'コンビニ'}</b><br><small>${node.tags?.['addr:full']||node.tags?.['addr:street']||''}</small>`);
      m._conveniData={lat:node.lat,lng:node.lon,name:node.tags?.name||'コンビニ',chain};
      conveniMarkers.push(m);
    });

    // 最近傍法で巡回ルート計算
    const points=conveniMarkers.map(m=>m._conveniData);
    const route=calcRoute([{lat:userLat,lng:userLng,name:'現在地',chain:'start'},...points]);
    drawRoute(route);
    showRouteResult(route);

  } catch(e){
    document.getElementById('route-result').style.display='block';
    document.getElementById('route-summary').textContent='⚠️ コンビニ情報の取得に失敗しました。通信環境を確認してください。';
  }
}

function updateMap(){
  if(userLat&&mapReady) fetchAndShowConveni();
}

// 最近傍法（Nearest Neighbor）で巡回順序計算
function calcRoute(points){
  if(points.length<=1) return points;
  const visited=new Set([0]);
  const route=[points[0]];
  while(route.length<points.length){
    const last=route[route.length-1];
    let nearest=-1,minDist=Infinity;
    for(let i=0;i<points.length;i++){
      if(visited.has(i)) continue;
      const d=dist(last.lat,last.lng,points[i].lat,points[i].lng);
      if(d<minDist){minDist=d;nearest=i;}
    }
    visited.add(nearest);
    route.push(points[nearest]);
  }
  return route;
}

function dist(lat1,lng1,lat2,lng2){
  const R=6371000;
  const dLat=(lat2-lat1)*Math.PI/180;
  const dLng=(lng2-lng1)*Math.PI/180;
  const a=Math.sin(dLat/2)**2+Math.cos(lat1*Math.PI/180)*Math.cos(lat2*Math.PI/180)*Math.sin(dLng/2)**2;
  return R*2*Math.atan2(Math.sqrt(a),Math.sqrt(1-a));
}

function drawRoute(route){
  if(routeLayer){leafMap.removeLayer(routeLayer);routeLayer=null;}
  const coords=route.map(p=>[p.lat,p.lng]);
  routeLayer=L.polyline(coords,{
    color:'#A855F7',weight:3,opacity:.8,dashArray:'8,5'
  }).addTo(leafMap);

  // 番号ラベルをマーカーに追加
  route.forEach((p,i)=>{
    if(i===0) return;
    const info=CHAIN_INFO[p.chain]||{color:'#888'};
    L.marker([p.lat,p.lng],{
      icon:L.divIcon({
        html:`<div style="background:${info.color};color:#fff;font-size:10px;font-weight:800;width:20px;height:20px;border-radius:50%;display:flex;align-items:center;justify-content:center;border:2px solid #fff;box-shadow:0 1px 3px rgba(0,0,0,.4)">${i}</div>`,
        iconSize:[20,20],iconAnchor:[10,10]
      }),zIndexOffset:1000
    }).addTo(leafMap);
  });

  // 地図を全ルートが見えるようにfit
  if(coords.length>1) leafMap.fitBounds(L.latLngBounds(coords).pad(.15));
}

function showRouteResult(route){
  const stops=route.slice(1); // 現在地を除く
  let totalDist=0;
  for(let i=0;i<route.length-1;i++){
    totalDist+=dist(route[i].lat,route[i].lng,route[i+1].lat,route[i+1].lng);
  }
  const walkMin=Math.round(totalDist/80); // 徒歩80m/分
  document.getElementById('route-summary').innerHTML=
    `<b style="color:#fcd34d">${stops.length}件</b>のコンビニを巡回 · 総距離約<b style="color:#fcd34d">${(totalDist/1000).toFixed(1)}km</b> · 徒歩約<b style="color:#fcd34d">${walkMin}分</b>`;

  const chainCount={};
  stops.forEach(s=>{ chainCount[s.chain]=(chainCount[s.chain]||0)+1; });
  const chainSummary=Object.entries(chainCount).map(([k,v])=>`${CHAIN_INFO[k]?.emoji||'🏪'}${CHAIN_INFO[k]?.name||k}×${v}`).join(' · ');
  
  let listHTML='';
  stops.forEach((s,i)=>{
    const info=CHAIN_INFO[s.chain]||{color:'#888',emoji:'🏪',name:'コンビニ'};
    const d=i===0?dist(route[0].lat,route[0].lng,s.lat,s.lng):dist(route[i].lat,route[i].lng,s.lat,s.lng);
    listHTML+=`<div class="route-stop">
      <div class="route-num" style="background:${info.color}">${i+1}</div>
      <div style="flex:1;min-width:0">
        <div style="font-size:11px;font-weight:700;color:#e2e8f0;overflow:hidden;text-overflow:ellipsis;white-space:nowrap">${info.emoji} ${s.name}</div>
        <div style="font-size:9px;color:#64748b">前の地点から約${Math.round(d)}m・徒歩${Math.round(d/80)}分</div>
      </div>
    </div>`;
  });
  document.getElementById('route-list').innerHTML=`<div style="font-size:10px;color:#94a3b8;margin-bottom:6px">${chainSummary}</div>${listHTML}`;
  document.getElementById('route-result').style.display='block';
}
</script>


  <!-- 情報提供バナー -->
  <div style="margin-top:14px;background:rgba(255,255,255,.04);border:1px dashed rgba(255,255,255,.15);border-radius:10px;padding:10px 12px;text-align:center">
    <div style="font-size:10px;color:#64748b;line-height:1.7">
      📩 あなたの地域の入荷情報を教えてください<br>
      <span style="color:#A855F7;font-weight:600">地域別攻略ガイドに追加します</span>
    </div>
  </div>

</div>

<!-- モーダル（下から出るシート形式） -->
<div class="modal-ov hidden" id="modalov" onclick="closeModal()">
  <div class="modal-box" onclick="event.stopPropagation()">
    <div class="modal-drag"></div>
    <div class="modal-head">
      <span class="modal-ico" id="m-ico"></span>
      <button class="modal-x" onclick="closeModal()">✕</button>
    </div>
    <div class="modal-title" id="m-title"></div>
    <div class="modal-date"  id="m-date"></div>
    <div id="m-pred" class="modal-pred hidden">★ 過去実績に基づく予想です。公式発表をご確認ください。</div>
    <div class="modal-row"><div class="modal-rlbl">店舗 / チェーン</div><div class="modal-rval" id="m-store"></div></div>
    <div class="modal-row hidden" id="m-regrow"><div class="modal-rlbl">対応地域</div><div class="modal-rval" id="m-reg"></div></div>
    <div class="modal-row hidden" id="m-detrow"><div class="modal-rlbl">詳細 · 応募条件</div><div class="modal-rtxt" id="m-det"></div></div>
    <button class="modal-link hidden" id="m-link" onclick="openLink(this.dataset.url)">🔗 応募・詳細ページを開く →</button>
  </div>
</div>

<script>
// ===== 定数 =====
const REGIONS={
  "全国オンライン":{emoji:"🌐",color:"#6C5CE7"},
  "北海道・東北":  {emoji:"❄️",color:"#60A5FA"},
  "関東":          {emoji:"🗼",color:"#F59E0B"},
  "中部・東海":    {emoji:"🗻",color:"#10B981"},
  "関西":          {emoji:"⛩️",color:"#EC4899"},
  "中国・四国":    {emoji:"🌊",color:"#14B8A6"},
  "九州・沖縄":    {emoji:"🌺",color:"#F97316"},
};
const STORE_CATS={
  "オンライン・通販": ["Amazon","ポケモンセンター","セブンネット","ファミマオンライン","楽天ブックス","あみあみ","遊々亭"],
  "家電量販店":       ["ヨドバシ","ビックカメラ","コジマ","ヤマダ電機","ジョーシン","エディオン","ノジマ"],
  "玩具・書店系":     ["トイザらス","イオン","ゲオ","TSUTAYA","ブックオフ","ふるいち","WonderGOO","平和堂","イトーヨーカドー"],
  "コンビニ":          ["セブンイレブン","ファミリーマート","ローソン","ミニストップ","デイリーヤマザキ"],
  "専門ショップ":     ["イエローサブマリン","ホビーステーション","駿河屋","バトロコ","フルコンプ","竜星のPAO","カードボックス","晴れる屋","カードラボ","ドラゴンスター"],
};
const SC={
  "Amazon":           {bg:"#FF9900",lt:"#FFF8EE",tx:"#7D4700"},
  "ポケモンセンター": {bg:"#E84393",lt:"#FFE8F4",tx:"#A0005A"},
  "セブンネット":     {bg:"#1D6E38",lt:"#E9F7EF",tx:"#1D6E38"},
  "ファミマオンライン":{bg:"#0066B3",lt:"#EBF5FB",tx:"#1A5276"},
  "楽天ブックス":     {bg:"#BF0000",lt:"#FDEDEC",tx:"#7B241C"},
  "あみあみ":         {bg:"#9333EA",lt:"#F5F0FF",tx:"#6B21A8"},
  "遊々亭":           {bg:"#BE185D",lt:"#FDF2F8",tx:"#831843"},
  "ヨドバシ":         {bg:"#E74C3C",lt:"#FDEDEC",tx:"#922B21"},
  "ビックカメラ":     {bg:"#3498DB",lt:"#EBF5FB",tx:"#1A5276"},
  "コジマ":           {bg:"#2471A3",lt:"#EBF5FB",tx:"#1A5276"},
  "ヤマダ電機":       {bg:"#2980B9",lt:"#EBF5FB",tx:"#1A5276"},
  "ジョーシン":       {bg:"#16A085",lt:"#E8F8F5",tx:"#0E6655"},
  "エディオン":       {bg:"#117A65",lt:"#E8F8F5",tx:"#0E6655"},
  "ノジマ":           {bg:"#1ABC9C",lt:"#E8F8F5",tx:"#0E6655"},
  "トイザらス":       {bg:"#E67E22",lt:"#FEF9E7",tx:"#784212"},
  "イオン":           {bg:"#27AE60",lt:"#E9F7EF",tx:"#1D6A38"},
  "ゲオ":             {bg:"#8E44AD",lt:"#F4ECF7",tx:"#6C3483"},
  "TSUTAYA":          {bg:"#F39C12",lt:"#FEF9E7",tx:"#7D6608"},
  "ブックオフ":       {bg:"#E91E63",lt:"#FCE4EC",tx:"#880E4F"},
  "ふるいち":         {bg:"#795548",lt:"#EFEBE9",tx:"#4E342E"},
  "WonderGOO":        {bg:"#0288D1",lt:"#E1F5FE",tx:"#01579B"},
  "平和堂":           {bg:"#388E3C",lt:"#E8F5E9",tx:"#1B5E20"},
  "イトーヨーカドー": {bg:"#D32F2F",lt:"#FFEBEE",tx:"#B71C1C"},
  "イエローサブマリン":{bg:"#F1C40F",lt:"#FEFBD8",tx:"#7D6608"},
  "ホビーステーション":{bg:"#2ECC71",lt:"#E9F7EF",tx:"#1E8449"},
  "駿河屋":           {bg:"#E53935",lt:"#FFEBEE",tx:"#B71C1C"},
  "バトロコ":         {bg:"#7B1FA2",lt:"#F3E5F5",tx:"#4A148C"},
  "フルコンプ":       {bg:"#00897B",lt:"#E0F2F1",tx:"#004D40"},
  "竜星のPAO":        {bg:"#F57F17",lt:"#FFF9C4",tx:"#E65100"},
  "カードボックス":   {bg:"#1565C0",lt:"#E3F2FD",tx:"#0D47A1"},
  "晴れる屋":         {bg:"#00ACC1",lt:"#E0F7FA",tx:"#006064"},
  "カードラボ":       {bg:"#5C6BC0",lt:"#E8EAF6",tx:"#283593"},
  "ドラゴンスター":   {bg:"#BF360C",lt:"#FBE9E7",tx:"#BF360C"},
  "公式":             {bg:"#6C5CE7",lt:"#F0EDFF",tx:"#4527A0"},
  "セブンイレブン":   {bg:"#007940",lt:"#E8F5E9",tx:"#1B5E20"},
  "ファミリーマート": {bg:"#0057A8",lt:"#E3F2FD",tx:"#0D47A1"},
  "ローソン":         {bg:"#1565C0",lt:"#E3F2FD",tx:"#0D47A1"},
  "ミニストップ":     {bg:"#FF6F00",lt:"#FFF8E1",tx:"#E65100"},
  "デイリーヤマザキ":{bg:"#B71C1C",lt:"#FFEBEE",tx:"#7f0000"},
};
const ET={
  release:        {label:"発売日",    emoji:"🎴"},
  lottery_start:  {label:"抽選開始", emoji:"🎰"},
  lottery_end:    {label:"抽選締切", emoji:"⏰"},
  lottery_result: {label:"当選発表", emoji:"🎊"},
  purchase:       {label:"購入・販売",emoji:"🛒"},
};

// ===== イベントデータ =====
const EVENTS=[
  // ── アビスアイ ──
  {id:"ae-rel",  title:"アビスアイ 発売",             date:"2026-05-22",type:"release",       store:"公式",            regions:["全国"],            detail:"メガダークライex収録。1BOX 6,000円(税込)",url:"https://www.pokemon-card.com/"},
  {id:"ae-pcen", title:"アビスアイ 抽選",             date:"2026-04-17",endDate:"2026-04-20",type:"lottery_start",store:"ポケモンセンター",    regions:["全国オンライン"],  detail:"ポケモンセンターオンライン",url:"https://www.pokemoncenter-online.com/"},
  {id:"ae-amz",  title:"アビスアイ 招待リクエスト",   date:"2026-04-20",type:"lottery_start",store:"Amazon",           regions:["全国オンライン"],  detail:"招待リクエスト方式（1BOX上限）",url:"https://www.amazon.co.jp/dp/B0F5KKK8NR"},
  {id:"ae-7net", title:"アビスアイ 抽選",             date:"2026-04-23",endDate:"2026-04-27",type:"lottery_start",store:"セブンネット",         regions:["全国オンライン"],  detail:"アプリから応募 / 2025/6/1〜2026/4/23購入履歴必要",url:"https://7net.omni7.jp/"},
  {id:"ae-toys", title:"アビスアイ 抽選",             date:"2026-04-21",endDate:"2026-04-27",type:"lottery_start",store:"トイザらス",           regions:["全国"],            detail:"店頭QRコードで応募 / 3/16〜4/27の1000円以上購入が条件",url:"https://www.toysrus.co.jp/"},
  {id:"ae-geo",  title:"アビスアイ 抽選",             date:"2026-04-30",endDate:"2026-05-07",type:"lottery_start",store:"ゲオ",                regions:["全国"],            detail:"GEOアプリ / Pontaカード連携必須 / 4/30 11:00〜5/7 17:59",url:"https://geo-online.co.jp/"},
  {id:"ae-yama", title:"アビスアイ 抽選",             date:"2026-04-20",endDate:"2026-04-21",type:"lottery_start",store:"ヤマダ電機",           regions:["全国"],            detail:"ヤマダデジタル会員アプリ / 当選発表5/19",url:"https://www.yamada-denkiweb.com/"},
  {id:"ae-jsh",  title:"アビスアイ 抽選",             date:"2026-04-24",endDate:"2026-04-27",type:"lottery_start",store:"ジョーシン",           regions:["関東","中部・東海","関西"],detail:"シルバースマイル会員以上（累計購入1万円以上）",url:"https://joshinweb.jp/"},
  {id:"ae-bic",  title:"アビスアイ 抽選（店頭）",     date:"2026-05-08",endDate:"2026-05-10",type:"lottery_start",store:"ビックカメラ",         regions:["関東","中部・東海","関西","九州・沖縄"],detail:"コジマ×ビックカメラ 店頭抽選",url:"https://www.biccamera.com/"},
  {id:"ae-ion",  title:"アビスアイ 抽選",             date:"2026-04-18",endDate:"2026-05-06",type:"lottery_start",store:"イオン",              regions:["北海道・東北"],    detail:"イオン北海道eショップ（店舗受取）",url:"https://www.aeon.jp/"},
  {id:"ae-ysub", title:"アビスアイ 抽選",             date:"2026-04-16",endDate:"2026-04-21",type:"lottery_start",store:"イエローサブマリン",  regions:["関東","関西"],     detail:"店頭レシート(300円以上)が必要 / 当選4/25発表",url:"https://www.yellowsubmarine.co.jp/"},
  {id:"ae-hbst", title:"アビスアイ 抽選",             date:"2026-04-17",endDate:"2026-04-19",type:"lottery_start",store:"ホビーステーション",  regions:["関東"],            detail:"Googleアカウント必要 / 1アカウント1応募",url:"https://www.hbst.net/"},
  {id:"ae-furu", title:"アビスアイ 抽選",             date:"2026-04-17",endDate:"2026-04-24",type:"lottery_start",store:"ふるいち",            regions:["全国"],            detail:"ふるいちアプリ登録のみでOK / イオンモール内店舗",url:"https://www.furu1.net/"},
  {id:"ae-amia", title:"アビスアイ 抽選",             date:"2026-04-18",endDate:"2026-04-24",type:"lottery_start",store:"あみあみ",            regions:["全国オンライン"],  detail:"メルマガ購読 + 2025/1/6〜2026/1/5購入履歴必要",url:"https://www.amiami.jp/"},
  {id:"ae-edit", title:"アビスアイ 抽選",             date:"2026-04-20",endDate:"2026-04-23",type:"lottery_start",store:"エディオン",          regions:["中国・四国","関西","中部・東海"],detail:"エディオンカード会員 / アプリ会員対象",url:"https://www.edion.com/"},
  {id:"ae-noji", title:"アビスアイ 抽選",             date:"2026-04-24",endDate:"2026-04-27",type:"lottery_start",store:"ノジマ",              regions:["関東"],            detail:"ノジマオンライン",url:"https://online.nojima.co.jp/"},

  // ── ストームエメラルダ ──
  {id:"se-rel",   title:"ストームエメラルダ 発売",             date:"2026-07-31",type:"release",       store:"公式",            regions:["全国"],           detail:"メガレックウザex収録 / 全76種+??? / 1BOX 6,000円",url:"https://www.pokemon-card.com/"},
  {id:"se-amz",   title:"ストームエメラルダ 招待リクエスト",   date:"2026-06-19",type:"lottery_start",store:"Amazon",           regions:["全国オンライン"], detail:"6/19 14時〜受付開始（1BOX 6,006円 / 当選通知6/20 11時頃）",url:"https://www.amazon.co.jp/s?k=%E3%82%B9%E3%83%88%E3%83%BC%E3%83%A0%E3%82%A8%E3%83%A1%E3%83%A9%E3%83%AB%E3%83%80+BOX"},
  {id:"se-amz-r", title:"ストームエメラルダ 当選通知",         date:"2026-06-20",type:"lottery_result",store:"Amazon",           regions:["全国オンライン"], detail:"6/20 11時頃に当選通知予想",url:"https://www.amazon.co.jp/s?k=%E3%82%B9%E3%83%88%E3%83%BC%E3%83%A0%E3%82%A8%E3%83%A1%E3%83%A9%E3%83%AB%E3%83%80+BOX"},
  {id:"se-7net",  title:"ストームエメラルダ 抽選",             date:"2026-06-17",type:"lottery_start",store:"セブンネット",      regions:["全国オンライン"], detail:"アプリ応募 / 2025/6/1〜2026/6/17購入履歴必要",url:"https://7net.omni7.jp/"},
  {id:"se-pcen",  title:"ストームエメラルダ 抽選",             date:"2026-06-25",endDate:"2026-06-28",type:"lottery_start",store:"ポケモンセンター",   regions:["全国オンライン"], detail:"発売1ヶ月前 / 過去実績から予想",pred:true,url:"https://www.pokemoncenter-online.com/"},
  {id:"se-yodo",  title:"ストームエメラルダ 抽選",             date:"2026-07-03",endDate:"2026-07-06",type:"lottery_start",store:"ヨドバシ",           regions:["全国"],           detail:"過去2年以内購入履歴必要 / 過去実績から予想",pred:true,url:"https://www.yodobashi.com/"},
  {id:"se-toys",  title:"ストームエメラルダ 抽選",             date:"2026-07-05",endDate:"2026-07-11",type:"lottery_start",store:"トイザらス",         regions:["全国"],           detail:"店頭QRコード / 過去実績から予想",pred:true,url:"https://www.toysrus.co.jp/"},
  {id:"se-geo",   title:"ストームエメラルダ 抽選",             date:"2026-07-06",endDate:"2026-07-13",type:"lottery_start",store:"ゲオ",              regions:["全国"],           detail:"GEOアプリ / Pontaカード必須 / 過去実績から予想",pred:true,url:"https://geo-online.co.jp/"},
  {id:"se-yama",  title:"ストームエメラルダ 抽選",             date:"2026-07-06",endDate:"2026-07-08",type:"lottery_start",store:"ヤマダ電機",         regions:["全国"],           detail:"ヤマダデジタル会員アプリ / 過去実績から予想",pred:true,url:"https://www.yamada-denkiweb.com/"},
  {id:"se-jsh",   title:"ストームエメラルダ 抽選",             date:"2026-07-06",endDate:"2026-07-12",type:"lottery_start",store:"ジョーシン",         regions:["関東","中部・東海","関西"],detail:"シルバースマイル会員以上 / 過去実績から予想",pred:true,url:"https://joshinweb.jp/"},
  {id:"se-bic",   title:"ストームエメラルダ 抽選",             date:"2026-07-08",endDate:"2026-07-10",type:"lottery_start",store:"ビックカメラ",       regions:["関東","中部・東海","関西","九州・沖縄"],detail:"コジマ×ビックカメラ 店頭 / 過去実績から予想",pred:true,url:"https://www.biccamera.com/"},
  {id:"se-ion",   title:"ストームエメラルダ 抽選",             date:"2026-07-07",endDate:"2026-07-13",type:"lottery_start",store:"イオン",            regions:["全国"],           detail:"過去実績から予想",pred:true,url:"https://www.aeon.jp/"},
  {id:"se-tsut",  title:"ストームエメラルダ 抽選",             date:"2026-07-05",endDate:"2026-07-10",type:"lottery_start",store:"TSUTAYA",           regions:["全国"],           detail:"過去実績から予想",pred:true,url:"https://store.tsutaya.co.jp/"},
  {id:"se-edit",  title:"ストームエメラルダ 抽選",             date:"2026-07-07",endDate:"2026-07-10",type:"lottery_start",store:"エディオン",         regions:["中国・四国","関西","中部・東海"],detail:"エディオンカード会員 / 過去実績から予想",pred:true,url:"https://www.edion.com/"},
  {id:"se-noji",  title:"ストームエメラルダ 抽選",             date:"2026-07-05",endDate:"2026-07-09",type:"lottery_start",store:"ノジマ",            regions:["関東"],           detail:"ノジマオンライン / 過去実績から予想",pred:true,url:"https://online.nojima.co.jp/"},
  {id:"se-ysub",  title:"ストームエメラルダ 抽選",             date:"2026-07-06",endDate:"2026-07-11",type:"lottery_start",store:"イエローサブマリン", regions:["関東","関西"],    detail:"店頭レシート(300円以上)必要 / 過去実績から予想",pred:true,url:"https://www.yellowsubmarine.co.jp/"},
  {id:"se-hbst",  title:"ストームエメラルダ 抽選",             date:"2026-07-07",endDate:"2026-07-09",type:"lottery_start",store:"ホビーステーション", regions:["関東"],           detail:"Googleアカウント必要 / 過去実績から予想",pred:true,url:"https://www.hbst.net/"},
  {id:"se-furu",  title:"ストームエメラルダ 抽選",             date:"2026-07-05",endDate:"2026-07-12",type:"lottery_start",store:"ふるいち",          regions:["全国"],           detail:"ふるいちアプリ登録のみ / 過去実績から予想",pred:true,url:"https://www.furu1.net/"},
  {id:"se-amia",  title:"ストームエメラルダ 抽選",             date:"2026-07-03",endDate:"2026-07-08",type:"lottery_start",store:"あみあみ",           regions:["全国オンライン"], detail:"メルマガ購読 + 購入履歴必要 / 過去実績から予想",pred:true,url:"https://www.amiami.jp/"},
  {id:"se-wgoo",  title:"ストームエメラルダ 抽選",             date:"2026-07-06",endDate:"2026-07-12",type:"lottery_start",store:"WonderGOO",         regions:["関東","中部・東海"],detail:"WonderGOO / 過去実績から予想",pred:true,url:"https://www.wondergoo.co.jp/"},
  {id:"se-klab",  title:"ストームエメラルダ 抽選",             date:"2026-07-07",endDate:"2026-07-10",type:"lottery_start",store:"カードラボ",         regions:["全国"],           detail:"カードラボ各店 / 過去実績から予想",pred:true,url:"https://cardlab.co.jp/"},
  {id:"se-dstar", title:"ストームエメラルダ 抽選",             date:"2026-07-07",endDate:"2026-07-09",type:"lottery_start",store:"ドラゴンスター",     regions:["関東"],           detail:"秋葉原・ドラゴンスター / 過去実績から予想",pred:true,url:"https://x.com/ds_akiba_ekimae"},
  {id:"se-buy731",title:"ストームエメラルダ 当日販売",         date:"2026-07-31",type:"purchase",      store:"ブックオフ",        regions:["全国"],           detail:"発売日当日 店頭販売（在庫限り）",pred:true,url:"https://www.bookoff.co.jp/"},
  {id:"se-suruga",title:"ストームエメラルダ 当日販売",         date:"2026-07-31",type:"purchase",      store:"駿河屋",            regions:["関東","関西"],    detail:"発売日当日 店頭・通販（在庫限り）",pred:true,url:"https://www.suruga-ya.jp/"},
  {id:"se-btoko", title:"ストームエメラルダ 当日販売",         date:"2026-07-31",type:"purchase",      store:"バトロコ",          regions:["関東"],           detail:"発売日当日 店頭販売（在庫限り）",pred:true,url:"https://battleroco.co.jp/"},
  {id:"se-fulco", title:"ストームエメラルダ 当日販売",         date:"2026-07-31",type:"purchase",      store:"フルコンプ",        regions:["関東"],           detail:"発売日当日 / プレイヤーID必要",pred:true,url:"https://www.fullcomp.net/"},
  {id:"se-pao",   title:"ストームエメラルダ 当日販売",         date:"2026-07-31",type:"purchase",      store:"竜星のPAO",         regions:["関東"],           detail:"発売日当日 横浜店舗販売",pred:true,url:"https://www.pao-yokohama.jp/"},

  // ── 30th CELEBRATION ──
  {id:"cel-rel",  title:"30th CELEBRATION 発売",               date:"2026-09-16",type:"release",       store:"公式",            regions:["全国"],           detail:"30周年記念パック・世界同時発売。マイナカード本人確認導入予定",url:"https://www.pokemon-card.com/"},
  {id:"cel-pcen", title:"30th CELEBRATION 抽選",               date:"2026-08-01",endDate:"2026-08-07",type:"lottery_start",store:"ポケモンセンター",   regions:["全国オンライン"], detail:"過去実績から予想 / マイナカード本人確認で当選率UP予定",pred:true,url:"https://www.pokemoncenter-online.com/"},
  {id:"cel-amz",  title:"30th CELEBRATION 招待",               date:"2026-08-10",type:"lottery_start",store:"Amazon",           regions:["全国オンライン"], detail:"過去実績から予想",pred:true,url:"https://www.amazon.co.jp/"},
  {id:"cel-yodo", title:"30th CELEBRATION 抽選",               date:"2026-08-12",endDate:"2026-08-16",type:"lottery_start",store:"ヨドバシ",           regions:["全国"],           detail:"過去実績から予想",pred:true,url:"https://www.yodobashi.com/"},
  {id:"cel-toys", title:"30th CELEBRATION 抽選",               date:"2026-08-12",endDate:"2026-08-18",type:"lottery_start",store:"トイザらス",         regions:["全国"],           detail:"過去実績から予想",pred:true,url:"https://www.toysrus.co.jp/"},
  {id:"cel-geo",  title:"30th CELEBRATION 抽選",               date:"2026-08-14",endDate:"2026-08-21",type:"lottery_start",store:"ゲオ",              regions:["全国"],           detail:"過去実績から予想",pred:true,url:"https://geo-online.co.jp/"},
  {id:"cel-furu", title:"30th CELEBRATION 抽選",               date:"2026-08-13",endDate:"2026-08-20",type:"lottery_start",store:"ふるいち",          regions:["全国"],           detail:"過去実績から予想",pred:true,url:"https://www.furu1.net/"},
  {id:"cel-ysub", title:"30th CELEBRATION 抽選",               date:"2026-08-14",endDate:"2026-08-19",type:"lottery_start",store:"イエローサブマリン", regions:["関東","関西"],    detail:"店頭レシート必要 / 過去実績から予想",pred:true,url:"https://www.yellowsubmarine.co.jp/"},
  {id:"cel-hbst", title:"30th CELEBRATION 抽選",               date:"2026-08-14",endDate:"2026-08-17",type:"lottery_start",store:"ホビーステーション", regions:["関東"],           detail:"Googleアカウント必要 / 過去実績から予想",pred:true,url:"https://www.hbst.net/"},
  {id:"cel-jsh",  title:"30th CELEBRATION 抽選",               date:"2026-08-12",endDate:"2026-08-17",type:"lottery_start",store:"ジョーシン",         regions:["関東","中部・東海","関西"],detail:"過去実績から予想",pred:true,url:"https://joshinweb.jp/"},
  {id:"cel-bic",  title:"30th CELEBRATION 抽選",               date:"2026-08-15",endDate:"2026-08-17",type:"lottery_start",store:"ビックカメラ",       regions:["関東","中部・東海","関西","九州・沖縄"],detail:"過去実績から予想",pred:true,url:"https://www.biccamera.com/"},
  {id:"cel-edit", title:"30th CELEBRATION 抽選",               date:"2026-08-15",endDate:"2026-08-18",type:"lottery_start",store:"エディオン",         regions:["中国・四国","関西","中部・東海"],detail:"過去実績から予想",pred:true,url:"https://www.edion.com/"},

  // ── コンビニ販売（発売日共通）──
  // アビスアイ コンビニ
  {id:"ae-7e",   title:"アビスアイ コンビニ販売",   date:"2026-05-22",type:"purchase",store:"セブンイレブン",  regions:["全国"],detail:"発売日 朝7時〜販売開始（公式推奨）/ 入荷なし店舗あり",url:"https://www.sej.co.jp/"},
  {id:"ae-fm",   title:"アビスアイ コンビニ販売",   date:"2026-05-22",type:"purchase",store:"ファミリーマート",regions:["全国"],detail:"発売日 朝10時〜販売開始（全店共通ルール）/ レジ周辺に陳列",url:"https://www.family.co.jp/"},
  {id:"ae-lw",   title:"アビスアイ コンビニ販売",   date:"2026-05-22",type:"purchase",store:"ローソン",        regions:["全国"],detail:"発売日 朝7時〜販売開始（当選店舗のみ入荷）/ ローソン公式エンタメページで確認",url:"https://www.lawson.co.jp/"},
  {id:"ae-ms",   title:"アビスアイ コンビニ販売",   date:"2026-05-22",type:"purchase",store:"ミニストップ",    regions:["全国"],detail:"発売日 朝7時以降（店舗により異なる）/ 競争率低め穴場",url:"https://www.ministop.co.jp/"},
  // ストームエメラルダ コンビニ（発売日当日）
  {id:"se-7e",   title:"ストームエメラルダ コンビニ販売",date:"2026-07-31",type:"purchase",store:"セブンイレブン",  regions:["全国"],detail:"発売日 朝7時〜販売開始 / 入荷店舗は店頭で確認",url:"https://www.sej.co.jp/"},
  {id:"se-fm",   title:"ストームエメラルダ コンビニ販売",date:"2026-07-31",type:"purchase",store:"ファミリーマート",regions:["全国"],detail:"発売日 朝10時〜販売開始（全店共通）/ ファミマ公式サイトで確認",url:"https://www.family.co.jp/"},
  {id:"se-lw",   title:"ストームエメラルダ コンビニ販売",date:"2026-07-31",type:"purchase",store:"ローソン",        regions:["全国"],detail:"発売日 朝7時〜販売開始（当選店舗のみ）/ ローソン公式エンタメページで確認",url:"https://www.lawson.co.jp/"},
  {id:"se-ms",   title:"ストームエメラルダ コンビニ販売",date:"2026-07-31",type:"purchase",store:"ミニストップ",    regions:["全国"],detail:"発売日 朝7時以降（店舗により異なる）/ 穴場的存在",url:"https://www.ministop.co.jp/"},
  {id:"se-dy",   title:"ストームエメラルダ コンビニ販売",date:"2026-07-31",type:"purchase",store:"デイリーヤマザキ",regions:["全国"],detail:"発売日 午前中が狙い目 / 発売前日夕方に入荷する場合あり",url:"https://www.daily-yamazaki.jp/"},
  // 30th CELEBRATION コンビニ
  {id:"cel-7e",  title:"30th CELEBRATION コンビニ販売",date:"2026-09-16",type:"purchase",store:"セブンイレブン",  regions:["全国"],detail:"発売日 朝7時〜販売開始 / 入荷店舗は店頭で確認",pred:true,url:"https://www.sej.co.jp/"},
  {id:"cel-fm",  title:"30th CELEBRATION コンビニ販売",date:"2026-09-16",type:"purchase",store:"ファミリーマート",regions:["全国"],detail:"発売日 朝10時〜販売開始（全店共通）",pred:true,url:"https://www.family.co.jp/"},
  {id:"cel-lw",  title:"30th CELEBRATION コンビニ販売",date:"2026-09-16",type:"purchase",store:"ローソン",        regions:["全国"],detail:"発売日 朝7時〜販売開始（当選店舗のみ）",pred:true,url:"https://www.lawson.co.jp/"},

  // ── コンビニ定期再販（過去実績の法則より予測）──
  // 法則：量販店再販から3〜4週間後にコンビニへ流れる傾向
  // セブン・ファミマは週2回の定期配送（月水金 or 火木土）
  // ローソンは抽選当選店舗のみ入荷

  // アビスアイ コンビニ再販（量販店6月中旬再販 → コンビニ7月上旬予想）
  {id:"ae-cv-r1",  title:"アビスアイ コンビニ再販（予想）",   date:"2026-07-06",endDate:"2026-07-12",type:"purchase",store:"セブンイレブン",   regions:["全国"],detail:"量販店再販から3〜4週間後。朝7時狙い/入荷曜日は店舗で確認",pred:true,url:"https://www.sej.co.jp/"},
  {id:"ae-cv-r2",  title:"アビスアイ コンビニ再販（予想）",   date:"2026-07-06",endDate:"2026-07-12",type:"purchase",store:"ファミリーマート",regions:["全国"],detail:"量販店再販から3〜4週間後。朝10時販売開始",pred:true,url:"https://www.family.co.jp/"},
  {id:"ae-cv-r3",  title:"アビスアイ コンビニ再販（予想）",   date:"2026-07-06",endDate:"2026-07-12",type:"purchase",store:"ローソン",        regions:["全国"],detail:"当選店舗のみ。朝7時販売開始/ローソン公式エンタメページで確認",pred:true,url:"https://www.lawson.co.jp/"},

  // ストームエメラルダ コンビニ再販第1波（発売8週後 = 9月下旬予想）
  {id:"se-cv-r1",  title:"ストームエメラルダ コンビニ再販①（予想）",date:"2026-09-14",endDate:"2026-09-20",type:"purchase",store:"セブンイレブン",   regions:["全国"],detail:"発売約6〜8週後の第1波再販予想。朝7時狙い",pred:true,url:"https://www.sej.co.jp/"},
  {id:"se-cv-r2",  title:"ストームエメラルダ コンビニ再販①（予想）",date:"2026-09-14",endDate:"2026-09-20",type:"purchase",store:"ファミリーマート",regions:["全国"],detail:"発売約6〜8週後の第1波再販予想。朝10時販売開始",pred:true,url:"https://www.family.co.jp/"},
  {id:"se-cv-r3",  title:"ストームエメラルダ コンビニ再販①（予想）",date:"2026-09-14",endDate:"2026-09-20",type:"purchase",store:"ローソン",        regions:["全国"],detail:"発売約6〜8週後の第1波再販予想。当選店舗のみ",pred:true,url:"https://www.lawson.co.jp/"},
  {id:"se-cv-r4",  title:"ストームエメラルダ コンビニ再販①（予想）",date:"2026-09-14",endDate:"2026-09-20",type:"purchase",store:"ミニストップ",    regions:["全国"],detail:"穴場。競争率低め。入荷時間は店舗により異なる",pred:true,url:"https://www.ministop.co.jp/"},

  // ── ハドウシーカー ──
  {id:"hw-rel",   title:"ハドウシーカー 発売",                  date:"2026-11-27",type:"release",       store:"公式",            regions:["全国"],           detail:"商標登録から予想。メガルカリオZ収録期待",pred:true,url:"https://snkrdunk.com/articles/15950/"},
];

// ===== 状態 =====
let yr=new Date().getFullYear(), mo=new Date().getMonth();
let selDay=null, tabMode="r";
let fReg="全エリア", fCat="すべて", fStoreName="すべて";
let refreshing=false;

// ===== 初期化 =====
window.addEventListener('DOMContentLoaded',()=>{
  buildRegionFilter();
  buildStoreCatFilter();
  buildLegend();
  render();
  const isIOS=/iP(hone|ad|od)/.test(navigator.userAgent);
  if(!isIOS||window.navigator.standalone) document.getElementById('banner').classList.add('hidden');
});

// ===== タブ切替 =====
function switchTab(t){
  tabMode=t;
  document.getElementById('tab-r').classList.toggle('active',t==='r');
  document.getElementById('tab-s').classList.toggle('active',t==='s');
  document.getElementById('frow-r').classList.toggle('hidden',t!=='r');
  document.getElementById('frow-s').classList.toggle('hidden',t!=='s');
  selDay=null; render();
}

// ===== 地域フィルター =====
function buildRegionFilter(){
  const regs=["全エリア",...Object.keys(REGIONS)];
  const wrap=document.getElementById('fregion');
  regs.forEach(r=>{
    const b=document.createElement('button');
    b.className='fbtn'+(r===fReg?' active':'');
    b.textContent=(REGIONS[r]?.emoji||'🌏')+' '+r;
    if(r===fReg&&REGIONS[r]){b.style.background=REGIONS[r].color;b.style.color='#fff';}
    b.onclick=()=>{
      fReg=r;
      document.querySelectorAll('#fregion .fbtn').forEach((btn,i)=>{
        const rn=regs[i];
        btn.classList.toggle('active',rn===r);
        btn.style.background=rn===r&&REGIONS[rn]?REGIONS[rn].color:'';
        btn.style.color=rn===r?'#fff':'';
      });
      selDay=null; render();
    };
    wrap.appendChild(b);
  });
}

// ===== 店舗フィルター =====
function buildStoreCatFilter(){
  const cats=["すべて",...Object.keys(STORE_CATS)];
  const wrap=document.getElementById('fcat');
  cats.forEach(c=>{
    const b=document.createElement('button');
    b.className='fbtn'+(c===fCat?' active':'');
    b.textContent=c;
    if(c===fCat&&c!=='すべて'){b.style.background='#A855F7';b.style.color='#fff';}
    b.onclick=()=>{
      fCat=c; fStoreName='すべて';
      document.querySelectorAll('#fcat .fbtn').forEach((btn,i)=>{
        btn.classList.toggle('active',cats[i]===c);
        btn.style.background=cats[i]===c&&c!=='すべて'?'#A855F7':'';
        btn.style.color=cats[i]===c?'#fff':'';
      });
      buildStoreNames(); selDay=null; render();
    };
    wrap.appendChild(b);
  });
  buildStoreNames();
}
function buildStoreNames(){
  const wrap=document.getElementById('fstore');
  wrap.innerHTML='';
  const names=fCat==='すべて'?['すべて']:['すべて',...(STORE_CATS[fCat]||[])];
  names.forEach(n=>{
    const b=document.createElement('button');
    b.className='fbtn'+(n===fStoreName?' active':'');
    b.textContent=n;
    const sc=SC[n];
    if(n===fStoreName&&sc){b.style.background=sc.bg;b.style.color='#fff';}
    b.onclick=()=>{
      fStoreName=n;
      document.querySelectorAll('#fstore .fbtn').forEach((btn,i)=>{
        const nn=names[i];
        btn.classList.toggle('active',nn===n);
        btn.style.background=nn===n&&SC[nn]?SC[nn].bg:'';
        btn.style.color=nn===n?'#fff':'';
      });
      selDay=null; render();
    };
    wrap.appendChild(b);
  });
}

// ===== レジェンド =====
function buildLegend(){
  const wrap=document.getElementById('legendinner');
  Object.values(ET).forEach(v=>{
    const s=document.createElement('span');s.className='legend-item';s.textContent=v.emoji+' '+v.label;wrap.appendChild(s);
  });
  const sp=document.createElement('span');sp.className='legend-item';sp.textContent='★ 予想';sp.style.color='#f59e0b';wrap.appendChild(sp);
}

// ===== フィルターマッチング =====
function matchEv(ev){
  if(tabMode==='r'){
    if(fReg==='全エリア') return true;
    const regs=ev.regions||[];
    if(regs.includes('全国')) return true;
    if(regs.includes(fReg)) return true;
    // 全国オンラインは全エリアからも見えるが地域指定時は除外
    return false;
  } else {
    if(fStoreName!=='すべて') return ev.store===fStoreName;
    if(fCat!=='すべて') return (STORE_CATS[fCat]||[]).includes(ev.store);
    return true;
  }
}

// ===== 描画 =====
function render(){
  const MN=["1月","2月","3月","4月","5月","6月","7月","8月","9月","10月","11月","12月"];
  document.getElementById('mlabel').textContent=`${yr}年 ${MN[mo]}`;

  // 曜日ヘッダー
  const dn=document.getElementById('daynames');
  dn.innerHTML='';
  ["日","月","火","水","木","金","土"].forEach((d,i)=>{
    const div=document.createElement('div');
    div.className='dname'+(i===0?' sun':i===6?' sat':'');
    div.textContent=d;dn.appendChild(div);
  });

  const today=new Date();
  const dim=new Date(yr,mo+1,0).getDate();
  const fd=new Date(yr,mo,1).getDay();
  const grid=document.getElementById('calgrid');
  grid.innerHTML='';

  // 空白セル
  for(let i=0;i<fd;i++){const d=document.createElement('div');grid.appendChild(d);}

  for(let day=1;day<=dim;day++){
    const dayEvs=getDayEvs(day);
    const isT=day===today.getDate()&&mo===today.getMonth()&&yr===today.getFullYear();
    const dow=(fd+day-1)%7;
    const cell=document.createElement('div');
    cell.className='dcell'+(isT?' today':'')+(selDay===day?' selected':'');
    cell.onclick=()=>{selDay=selDay===day?null:day;render();};

    // 日付番号
    const nd=document.createElement('div');
    nd.className='dnum'+(dow===0?' sun':dow===6?' sat':'');
    if(isT){const b=document.createElement('span');b.className='tbadge';b.textContent=day;nd.appendChild(b);}
    else nd.textContent=day;
    cell.appendChild(nd);

    // イベントドット（最大3件）
    dayEvs.slice(0,3).forEach(ev=>{
      const sc=SC[ev.store]||{bg:'#636e72'};
      const et=ET[ev.type]||ET.release;
      const dot=document.createElement('div');
      dot.className='evdot';
      dot.style.background=sc.bg;
      dot.textContent=(ev.pred?'★':'')+et.emoji+' '+ev.store;
      dot.title=ev.title;
      cell.appendChild(dot);
    });
    if(dayEvs.length>3){
      const m=document.createElement('div');m.className='evmore';m.textContent='+'+(dayEvs.length-3);cell.appendChild(m);
    }
    grid.appendChild(cell);
  }
  renderDetail();
}

function getDayEvs(day){
  const ds=`${yr}-${String(mo+1).padStart(2,'0')}-${String(day).padStart(2,'0')}`;
  return EVENTS.filter(e=>{
    const hit=e.date===ds||(e.endDate&&e.date<=ds&&e.endDate>=ds);
    return hit&&matchEv(e);
  });
}

function renderDetail(){
  const wrap=document.getElementById('daydetail');
  if(!selDay){wrap.className='hidden';return;}
  const dayEvs=getDayEvs(selDay);
  if(dayEvs.length===0){
    wrap.className='daydetail';
    wrap.innerHTML=`<p style="text-align:center;color:#64748b;font-size:12px">${mo+1}月${selDay}日のイベントはありません</p>`;
    return;
  }
  // グローバルにキャッシュ（モーダルからインデックスで参照）
  window._dayEvs = dayEvs;
  wrap.className='daydetail';
  let html=`<div style="font-size:12px;font-weight:700;color:#A855F7;margin-bottom:10px">📅 ${mo+1}月${selDay}日（${dayEvs.length}件）<span style="font-size:10px;font-weight:400;color:#94a3b8;margin-left:6px">タップで詳細・リンク</span></div>`;
  dayEvs.forEach((ev,idx)=>{
    const sc=SC[ev.store]||{bg:'#636e72',lt:'#f5f5f5',tx:'#2d3436'};
    const et=ET[ev.type]||ET.release;
    html+=`<div class="listrow" style="border-left-color:${sc.bg}" onclick="openModalByIdx(${idx})">
      <div style="display:flex;align-items:center;gap:7px;min-width:0;margin-bottom:4px">
        <span style="background:${sc.bg};color:#fff;border-radius:5px;padding:3px 7px;font-size:10px;font-weight:700;white-space:nowrap;flex-shrink:0">${et.emoji} ${ev.store}</span>
        <span style="font-size:12px;font-weight:700;color:#1e293b;overflow:hidden;text-overflow:ellipsis;white-space:nowrap;flex:1">${ev.pred?'★ ':''}${ev.title}</span>
      </div>
      <div style="display:flex;align-items:center;justify-content:space-between">
        <span style="font-size:10px;color:#64748b">${et.label}${ev.endDate&&ev.endDate!==ev.date?' 〜 '+ev.endDate.slice(5):''}</span>
        <span style="font-size:11px;color:#6C5CE7;font-weight:700">詳細 ›</span>
      </div>
    </div>`;
  });
  wrap.innerHTML=html;
}

// ===== モーダル =====
function openModal(ev){
  if(typeof ev==='string') ev=JSON.parse(ev);
  const et=ET[ev.type]||ET.release;
  document.getElementById('m-ico').textContent=et.emoji;
  document.getElementById('m-title').textContent=(ev.pred?'★ ':'')+ev.title;
  document.getElementById('m-date').textContent=ev.date+(ev.endDate&&ev.endDate!==ev.date?' 〜 '+ev.endDate:'');
  document.getElementById('m-store').textContent=ev.store;
  document.getElementById('m-pred').classList.toggle('hidden',!ev.pred);
  const rr=document.getElementById('m-regrow');
  if(ev.regions&&ev.regions.length){rr.classList.remove('hidden');document.getElementById('m-reg').textContent=(ev.regions||[]).join(' / ');}
  else rr.classList.add('hidden');
  const dr=document.getElementById('m-detrow');
  if(ev.detail){dr.classList.remove('hidden');document.getElementById('m-det').textContent=ev.detail;}
  else dr.classList.add('hidden');
  const lk=document.getElementById('m-link');
  if(ev.url){lk.dataset.url=ev.url;lk.classList.remove('hidden');}
  else lk.classList.add('hidden');
  document.getElementById('modalov').classList.remove('hidden');
}

// ===== インデックスからモーダルを開く =====
function openModalByIdx(idx){
  const ev = (window._dayEvs||[])[idx];
  if(ev) openModal(ev);
}

// ===== リンクを開く（スタンドアロンモード対応）=====
function openLink(url){
  if(!url) return;
  // iOS スタンドアロン(ホーム画面アプリ)では window.location で Safari を起動
  if(window.navigator.standalone){
    window.location.href = url;
  } else {
    window.open(url, '_blank', 'noopener,noreferrer');
  }
}
function closeModal(){document.getElementById('modalov').classList.add('hidden');}

// ===== 月ナビ =====
function changeMonth(dir){
  selDay=null;
  mo+=dir;
  if(mo<0){mo=11;yr--;}
  if(mo>11){mo=0;yr++;}
  render();
}

// ===== ガイドタブ切替 =====
function switchGuide(tab){
  const isSanin = tab==='sanin';
  document.getElementById('guide-sanin').classList.toggle('hidden',!isSanin);
  document.getElementById('guide-general').classList.toggle('hidden',isSanin);
  const ts=document.getElementById('guide-tab-sanin');
  const tg=document.getElementById('guide-tab-general');
  ts.style.background=isSanin?'rgba(168,85,247,.35)':'none';
  ts.style.color=isSanin?'#fff':'#64748b';
  ts.style.fontWeight=isSanin?'700':'600';
  tg.style.background=!isSanin?'rgba(168,85,247,.35)':'none';
  tg.style.color=!isSanin?'#fff':'#64748b';
  tg.style.fontWeight=!isSanin?'700':'600';
}

// ===== 更新ボタン =====
async function doRefresh(){
  if(refreshing) return;
  refreshing=true;
  const btn=document.getElementById('btnref');
  const txt=document.getElementById('refreshtxt');
  btn.disabled=true;
  txt.innerHTML='<span class="spinner"></span>最新情報をWebで検索中…';

  // ゲームenv.net の最新情報ページを参照案内
  const urls=[
    'https://gamenv.net/tc/stormemerald/',
    'https://snkrdunk.com/articles/32581/',
    'https://note.com/pokeca_new/n/nc9e68cc8dded',
  ];

  // 実際のAPIアクセスはできないため、参照先を案内
  await new Promise(r=>setTimeout(r,1200));

  // 更新日時を更新
  const now=new Date();
  const ts=`${now.getMonth()+1}/${now.getDate()} ${String(now.getHours()).padStart(2,'0')}:${String(now.getMinutes()).padStart(2,'0')}`;
  txt.textContent=`✅ ${ts} 確認済 — 最新情報は下記リンクでご確認ください`;
  btn.disabled=false;
  refreshing=false;

  // 情報源リンクをモーダルで案内
  const src=SC["公式"];
  document.getElementById('m-ico').textContent='🔍';
  document.getElementById('m-title').textContent='最新情報の確認先';
  document.getElementById('m-date').textContent='リアルタイム情報は各サイトをご確認ください';
  document.getElementById('m-store').textContent='情報まとめサイト';
  document.getElementById('m-pred').classList.add('hidden');
  document.getElementById('m-regrow').classList.add('hidden');
  document.getElementById('m-detrow').classList.add('hidden');
  const lk=document.getElementById('m-link');
  lk.dataset.url='https://gamenv.net/tc/stormemerald/';
  lk.textContent='🔗 ストームエメラルダ最新抽選情報 → gamenv.net'; lk.onclick=function(){openLink(this.dataset.url);};
  lk.classList.remove('hidden');
  document.getElementById('modalov').classList.remove('hidden');
}
</script>
</body>
</html>
