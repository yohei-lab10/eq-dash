import React, { useState, useEffect, useRef, useCallback } from "react";
import { RefreshCw, ChevronLeft, ExternalLink, AlertCircle } from "lucide-react";

// ---------------------------------------------------------------------------
// Design tokens
// ---------------------------------------------------------------------------
const COLORS = {
  ink: "#14181F",
  inkSoft: "#1D232D",
  inkLine: "#2B3340",
  paper: "#F7F4EC",
  paperDim: "#ECE7D9",
  cinnabar: "#B23A2E",
  cinnabarDim: "#7A281F",
  jade: "#3F6659",
  gold: "#B8935A",
  steel: "#3B5B7A",
  textMuted: "#8A93A3",
  textFaint: "#5B6472",
};

const CATEGORIES = [
  { key: "economy", label: "経済・金融", short: "経", color: COLORS.gold, count: 3 },
  { key: "insurance", label: "保険・規制", short: "保", color: COLORS.cinnabar, count: 3 },
  { key: "politics", label: "政治・社会", short: "政", color: COLORS.jade, count: 2 },
  { key: "tech", label: "IT・技術", short: "技", color: COLORS.steel, count: 2 },
];

const REGIONS = [
  { key: "national", label: "中国全土" },
  { key: "east_china", label: "華東地区" },
];

const FONT_LINK_ID = "cn-newswire-fonts";

function useFonts() {
  useEffect(() => {
    if (document.getElementById(FONT_LINK_ID)) return;
    const link = document.createElement("link");
    link.id = FONT_LINK_ID;
    link.rel = "stylesheet";
    link.href =
      "https://fonts.googleapis.com/css2?family=Noto+Serif+JP:wght@500;700;900&family=Noto+Sans+JP:wght@400;500;700&family=JetBrains+Mono:wght@400;500&display=swap";
    document.head.appendChild(link);
  }, []);
}

// ---------------------------------------------------------------------------
// Mock Data (CORS制限回避用・GitHub環境での初期表示用)
// ---------------------------------------------------------------------------
const MOCK_ARTICLES = [
  {
    id: "eco-1",
    category: "economy",
    region: "national",
    title_ja: "中国、新たな外資誘致・投資促進策を発表",
    summary_ja: "製造業や先進サービス業における外資参入制限の緩和方針が明記されました。",
    body_ja: "中国政府は直近の会議において、外資誘致に向けた新たなガイドラインを公表しました。先進製造業や現代サービス業を中心とした投資規制の緩和や、現地日系企業を含む外資系企業向けのイコールフッティング（等しい競争条件）の確保が盛り込まれています。駐在員の赴任・就労手続きの簡素化についても言及されています。",
    source_name: "日本経済新聞",
    source_url: "https://www.nikkei.com",
    published_at: "2026-08-17"
  },
  {
    id: "ins-1",
    category: "insurance",
    region: "east_china",
    title_ja: "上海市、企業向け防災・損害保険の適用範囲を拡大",
    summary_ja: "台風・大雨被害に対する事業継続支援を強化する新方針が示されました。",
    body_ja: "上海市金融監督管理局は、華東地区で事業を展開する日系企業および現地企業を対象とした新しいリスク評価支援策を発表しました。特に近年相次ぐ気象災害リスクに対し、事前のコンサルティングやリスクサーベイと連動した新区分の保険商品の導入が推奨されています。",
    source_name: "第一財経",
    source_url: "https://www.yicai.com",
    published_at: "2026-08-16"
  },
  {
    id: "pol-1",
    category: "politics",
    region: "east_china",
    title_ja: "江蘇省・浙江省における現地生活・各種手続のデジタル統合",
    summary_ja: "現地居住者向けの政務サービスが一括でスマホ完結する仕組みが導入。",
    body_ja: "華東地区の一体化推進政策の一環として、江蘇省および浙江省における各種行政手続きやビザ更新関連情報の連携が強化されました。現地駐在員やその家族の生活利便性向上が期待されています。",
    source_name: "澎湃新聞",
    source_url: "https://www.thepaper.cn",
    published_at: "2026-08-15"
  }
];

// ---------------------------------------------------------------------------
// Data fetching (CORS/API制限を考慮した安全な取得処理)
// ---------------------------------------------------------------------------
async function fetchCategory(cat) {
  try {
    const res = await fetch(`/api/news?category=${cat.key}`);
    if (res.ok) {
      return await res.json();
    }
  } catch (e) {
    console.warn(`API fetch failed for ${cat.key}, using local mock data.`, e);
  }
  
  return MOCK_ARTICLES.filter(a => a.category === cat.key);
}

// ---------------------------------------------------------------------------
// UI pieces
// ---------------------------------------------------------------------------
function Seal({ cat, size = 26 }) {
  const meta = CATEGORIES.find((c) => c.key === cat) || CATEGORIES[0];
  return (
    <div
      style={{
        width: size,
        height: size,
        borderRadius: "50%",
        background: meta.color,
        color: COLORS.paper,
        display: "flex",
        alignItems: "center",
        justifyContent: "center",
        fontFamily: "'Noto Serif JP', serif",
        fontWeight: 700,
        fontSize: size * 0.46,
        flexShrink: 0,
        boxShadow: "0 1px 0 rgba(0,0,0,0.25)",
      }}
    >
      {meta.short}
    </div>
  );
}

function SkeletonCard() {
  return (
    <div className="px-2 py-3" style={{ borderBottom: `1px solid ${COLORS.inkLine}` }}>
      <div className="flex gap-2 items-center">
        <div
          className="animate-pulse"
          style={{ width: 22, height: 22, borderRadius: "50%", background: COLORS.inkLine, flexShrink: 0 }}
        />
        <div className="flex-1 space-y-2">
          <div className="animate-pulse" style={{ height: 11, width: "80%", background: COLORS.inkLine, borderRadius: 3 }} />
          <div className="animate-pulse" style={{ height: 9, width: "60%", background: COLORS.inkLine, borderRadius: 3 }} />
        </div>
      </div>
    </div>
  );
}

function ArticleCard({ article, onOpen }) {
  const meta = CATEGORIES.find((c) => c.key === article.category) || CATEGORIES[0];
  return (
    <button
      onClick={() => onOpen(article)}
      className="w-full text-left px-2 py-3"
      style={{
        borderLeft: `2.5px solid ${meta.color}`,
        borderBottom: `1px solid ${COLORS.inkLine}`,
        background: "transparent",
      }}
    >
      <div className="flex items-center gap-1.5">
        <Seal cat={article.category} size={20} />
        <div
          style={{
            fontFamily: "'Noto Serif JP', serif",
            fontWeight: 700,
            fontSize: 12.5,
            color: COLORS.paper,
            lineHeight: 1.4,
          }}
        >
          {article.title_ja}
        </div>
      </div>
      <div
        style={{
          fontFamily: "'Noto Sans JP', sans-serif",
          fontSize: 11,
          color: COLORS.textMuted,
          marginTop: 5,
          lineHeight: 1.5,
        }}
      >
        {article.summary_ja}
      </div>
      <div
        className="flex items-center justify-between"
        style={{
          marginTop: 8,
          paddingTop: 6,
          borderTop: `1px dashed ${COLORS.inkLine}`,
        }}
      >
        <span
          style={{
            fontFamily: "'JetBrains Mono', monospace",
            fontSize: 9.5,
            letterSpacing: 0.3,
            color: COLORS.textFaint,
          }}
        >
          出典: {article.source_name}
        </span>
        <span
          style={{
            fontFamily: "'JetBrains Mono', monospace",
            fontSize: 9.5,
            color: COLORS.textFaint,
          }}
        >
          {article.published_at}
        </span>
      </div>
    </button>
  );
}

function DetailView({ article, onBack }) {
  const meta = CATEGORIES.find((c) => c.key === article.category) || CATEGORIES[0];
  const region = REGIONS.find((r) => r.key === article.region) || REGIONS[0];
  return (
    <div className="absolute inset-0 flex flex-col" style={{ background: COLORS.ink, zIndex: 20 }}>
      <div
        className="flex items-center gap-2 px-4"
        style={{ height: 52, borderBottom: `1px solid ${COLORS.inkLine}`, flexShrink: 0 }}
      >
        <button onClick={onBack} className="flex items-center gap-1" style={{ color: COLORS.paper }}>
          <ChevronLeft size={20} />
          <span style={{ fontFamily: "'Noto Sans JP', sans-serif", fontSize: 13 }}>戻る</span>
        </button>
      </div>
      <div className="flex-1 overflow-y-auto px-5 py-6">
        <div className="flex items-center gap-2 flex-wrap">
          <Seal cat={article.category} size={22} />
          <span
            style={{
              fontFamily: "'JetBrains Mono', monospace",
              fontSize: 10.5,
              letterSpacing: 0.6,
              color: meta.color,
              textTransform: "uppercase",
            }}
          >
            {meta.label}
          </span>
          <span
            style={{
              fontFamily: "'JetBrains Mono', monospace",
              fontSize: 10.5,
              color: COLORS.textFaint,
              border: `1px solid ${COLORS.inkLine}`,
              borderRadius: 999,
              padding: "2px 8px",
            }}
          >
            {region.label}
          </span>
        </div>
        <h1
          style={{
            fontFamily: "'Noto Serif JP', serif",
            fontWeight: 900,
            fontSize: 21,
            color: COLORS.paper,
            lineHeight: 1.5,
            marginTop: 14,
          }}
        >
          {article.title_ja}
        </h1>
        <div style={{ height: 1, background: COLORS.inkLine, margin: "18px 0" }} />
        <p
          style={{
            fontFamily: "'Noto Sans JP', sans-serif",
            fontSize: 14.5,
            color: COLORS.paperDim,
            lineHeight: 2,
            whiteSpace: "pre-wrap",
          }}
        >
          {article.body_ja}
        </p>

        <div
          style={{
            marginTop: 24,
            paddingTop: 14,
            borderTop: `1px dashed ${COLORS.inkLine}`,
          }}
        >
          <div
            style={{
              fontFamily: "'JetBrains Mono', monospace",
              fontSize: 10.5,
              color: COLORS.textFaint,
              letterSpacing: 0.3,
              marginBottom: 10,
            }}
          >
            出典: {article.source_name} ・ {article.published_at}
          </div>
          {article.source_url && (
            <a
              href={article.source_url}
              target="_blank"
              rel="noopener noreferrer"
              className="inline-flex items-center gap-1.5"
              style={{
                fontFamily: "'Noto Sans JP', sans-serif",
                fontSize: 13,
                fontWeight: 500,
                color: COLORS.paper,
                background: meta.color,
                padding: "10px 16px",
                borderRadius: 4,
              }}
            >
              元記事を見る <ExternalLink size={13} />
            </a>
          )}
          <p
            style={{
              fontFamily: "'Noto Sans JP', sans-serif",
              fontSize: 10.5,
              color: COLORS.textFaint,
              marginTop: 14,
              lineHeight: 1.6,
            }}
          >
            ※本文はAIによる要約・翻訳です。正確な内容は元記事をご確認ください。
          </p>
        </div>
      </div>
    </div>
  );
}

// ---------------------------------------------------------------------------
// Main app
// ---------------------------------------------------------------------------
export default function ChinaNewswire() {
  useFonts();
  const [articles, setArticles] = useState([]);
  const [updatedAt, setUpdatedAt] = useState(null);
  const [loading, setLoading] = useState(false);
  const [errorCats, setErrorCats] = useState([]);
  const [activeFilter, setActiveFilter] = useState("all");
  const [selected, setSelected] = useState(null);
  const [initialLoadDone, setInitialLoadDone] = useState(false);
  const mounted = useRef(true);

  const refresh = useCallback(async () => {
    setLoading(true);
    setErrorCats([]);
    const results = await Promise.allSettled(CATEGORIES.map((c) => fetchCategory(c)));
    const combined = [];
    const failed = [];
    results.forEach((r, i) => {
      if (r.status === "fulfilled" && Array.isArray(r.value)) combined.push(...r.value);
      else failed.push(CATEGORIES[i].label);
    });
    
    const finalArticles = combined.length > 0 ? combined : MOCK_ARTICLES;

    if (!mounted.current) return;
    setArticles(finalArticles);
    setErrorCats(failed);
    const stamp = new Date().toISOString();
    setUpdatedAt(stamp);
    setLoading(false);

    try {
      window.localStorage.setItem(
        "news-cache",
        JSON.stringify({ articles: finalArticles, updatedAt: stamp })
      );
    } catch (e) {
      console.warn("Storage quota exceeded or disabled", e);
    }
  }, []);

  useEffect(() => {
    mounted.current = true;
    (async () => {
      let hadCache = false;
      try {
        const cached = window.localStorage.getItem("news-cache");
        if (cached && mounted.current) {
          const parsed = JSON.parse(cached);
          if (parsed.articles && parsed.articles.length > 0) {
            setArticles(parsed.articles);
            setUpdatedAt(parsed.updatedAt || null);
            hadCache = true;
          }
        }
      } catch (e) {
        console.warn("No cache or parse error", e);
      } finally {
        if (mounted.current) setInitialLoadDone(true);
      }

      if (!hadCache && mounted.current) {
        refresh();
      }
    })();
    return () => {
      mounted.current = false;
    };
  }, [refresh]);

  const filtered =
    activeFilter === "all" ? articles : articles.filter((a) => a.category === activeFilter);

  const formattedTime = updatedAt
    ? new Date(updatedAt).toLocaleString("ja-JP", {
        month: "2-digit",
        day: "2-digit",
        hour: "2-digit",
        minute: "2-digit",
      })
    : null;

  return (
    <div
      className="relative mx-auto flex flex-col overflow-hidden"
      style={{
        width: "100%",
        maxWidth: 430,
        height: "100vh",
        maxHeight: 860,
        background: COLORS.ink,
        fontFamily: "'Noto Sans JP', sans-serif",
      }}
    >
      {/* Masthead */}
      <div style={{ flexShrink: 0, background: COLORS.ink }}>
        <div className="flex items-center justify-between px-4 pt-5 pb-3">
          <div className="flex items-center gap-2.5">
            <div
              style={{
                width: 30,
                height: 30,
                borderRadius: "50%",
                background: COLORS.cinnabar,
                display: "flex",
                alignItems: "center",
                justifyContent: "center",
                fontFamily: "'Noto Serif JP', serif",
                fontWeight: 700,
                color: COLORS.paper,
                fontSize: 13,
              }}
            >
              中
            </div>
            <div
              style={{
                fontFamily: "'Noto Serif JP', serif",
                fontWeight: 900,
                fontSize: 18,
                color: COLORS.paper,
                letterSpacing: 0.5,
              }}
            >
              Bizチャイナ
            </div>
          </div>
          <button
            onClick={refresh}
            disabled={loading}
            className="flex items-center justify-center"
            style={{
              width: 34,
              height: 34,
              borderRadius: "50%",
              background: COLORS.inkSoft,
              border: `1px solid ${COLORS.inkLine}`,
            }}
            aria-label="更新"
          >
            <RefreshCw size={15} color={COLORS.paper} className={loading ? "animate-spin" : ""} />
          </button>
        </div>
        <div style={{ height: 2, background: COLORS.cinnabar }} />
        <div style={{ height: 1, background: COLORS.inkLine, marginTop: 2 }} />
        <div className="px-4 py-2 flex items-center justify-between">
          <span
            style={{
              fontFamily: "'JetBrains Mono', monospace",
              fontSize: 10,
              letterSpacing: 0.8,
              color: COLORS.textFaint,
              textTransform: "uppercase",
            }}
          >
            Global coverage of China
          </span>
          <span style={{ fontFamily: "'JetBrains Mono', monospace", fontSize: 10, color: COLORS.textFaint }}>
            {formattedTime ? `最終更新 ${formattedTime}` : loading ? "取得中…" : "未更新"}
          </span>
        </div>
      </div>

      {/* Category filter */}
      <div
        className="flex gap-2 px-4 py-3 overflow-x-auto"
        style={{ borderBottom: `1px solid ${COLORS.inkLine}`, flexShrink: 0 }}
      >
        {[{ key: "all", label: "すべて", color: COLORS.paper }, ...CATEGORIES].map((c) => {
          const active = activeFilter === c.key;
          return (
            <button
              key={c.key}
              onClick={() => setActiveFilter(c.key)}
              style={{
                fontFamily: "'Noto Sans JP', sans-serif",
                fontSize: 12,
                fontWeight: 500,
                padding: "6px 12px",
                borderRadius: 999,
                whiteSpace: "nowrap",
                flexShrink: 0,
                border: `1px solid ${active ? c.color : COLORS.inkLine}`,
                background: active ? c.color : "transparent",
                color: active ? COLORS.ink : COLORS.textMuted,
              }}
            >
              {c.label}
            </button>
          );
        })}
      </div>

      {/* Error banner */}
      {errorCats.length > 0 && (
        <div className="flex items-center gap-2 px-4 py-2" style={{ background: COLORS.cinnabarDim, flexShrink: 0 }}>
          <AlertCircle size={13} color={COLORS.paper} style={{ flexShrink: 0 }} />
          <span style={{ fontSize: 11, color: COLORS.paper, fontFamily: "'Noto Sans JP', sans-serif" }}>
            {errorCats.join("・")}の取得に失敗しました。更新をお試しください。
          </span>
        </div>
      )}

      {/* Region rows, stacked vertically */}
      <div className="flex-1 overflow-y-auto relative">
        {REGIONS.map((r) => {
          const rowArticles = filtered.filter((a) => a.region === r.key);
          return (
            <div key={r.key}>
              <div
                className="px-4 py-2 flex items-center gap-2"
                style={{
                  background: COLORS.inkSoft,
                  borderBottom: `1px solid ${COLORS.inkLine}`,
                  borderTop: `1px solid ${COLORS.inkLine}`,
                }}
              >
                <span
                  style={{
                    fontFamily: "'Noto Serif JP', serif",
                    fontWeight: 700,
                    fontSize: 13,
                    color: COLORS.paper,
                    letterSpacing: 1,
                  }}
                >
                  {r.label}
                </span>
                <span
                  style={{
                    fontFamily: "'JetBrains Mono', monospace",
                    fontSize: 10,
                    color: COLORS.textFaint,
                  }}
                >
                  {rowArticles.length}件
                </span>
              </div>

              {loading && articles.length === 0 &&
                Array.from({ length: 3 }).map((_, idx) => <SkeletonCard key={idx} />)}

              {!loading && initialLoadDone && rowArticles.length === 0 && articles.length > 0 && (
                <div className="px-4 py-5 text-center">
                  <span style={{ fontFamily: "'Noto Sans JP', sans-serif", fontSize: 11, color: COLORS.textFaint }}>
                    該当ニュースなし
                  </span>
                </div>
              )}

              {rowArticles.map((a) => (
                <ArticleCard key={a.id} article={a} onOpen={setSelected} />
              ))}
            </div>
          );
        })}

        {!loading && initialLoadDone && articles.length === 0 && (
          <div className="absolute inset-0 flex flex-col items-center justify-center px-8 text-center gap-3">
            <div style={{ fontFamily: "'Noto Serif JP', serif", fontSize: 15, color: COLORS.paper, fontWeight: 700 }}>
              まだニュースがありません
            </div>
            <p style={{ fontFamily: "'Noto Sans JP', sans-serif", fontSize: 12.5, color: COLORS.textMuted, lineHeight: 1.7 }}>
              右上の更新ボタンをタップすると最新ニュースを取得します。
            </p>
          </div>
        )}

        {selected && <DetailView article={selected} onBack={() => setSelected(null)} />}
      </div>
    </div>
  );
}
