//@version=6
// ===== فاز ۱: اندیکاتور تشخیص/نمایش استراتژی ICT (بخش ۵ و ۱۱ سند مشخصات) =====
indicator("ICT Strategy — Phase 1", "ICT Ph1", overlay=true, max_lines_count=500, max_labels_count=500, max_boxes_count=500)

// ─────────────────────────────────────────────────────────────────────────────
// تنظیمات تایمفریم‌ها (تحلیل چندتایم‌فریمی)
// ─────────────────────────────────────────────────────────────────────────────
grpTf = "تایم‌فریم‌ها"
InpTF_M  = input.timeframe("M",   "تایم‌فریم ماهانه",  group=grpTf)
InpTF_W  = input.timeframe("W",   "تایم‌فریم هفتگی",  group=grpTf)
InpTF_D  = input.timeframe("D",   "تایم‌فریم روزانه",  group=grpTf)
InpTF_4H = input.timeframe("240", "تایم‌فریم ۴ ساعته", group=grpTf)
InpTF_1H = input.timeframe("60",  "تایم‌فریم ۱ ساعته", group=grpTf)
InpShowM  = input.bool(true,  "فعال‌سازی ماهانه",  group=grpTf)
InpShowW  = input.bool(true,  "فعال‌سازی هفتگی",  group=grpTf)
InpShowD  = input.bool(true,  "فعال‌سازی روزانه",  group=grpTf)
InpShow4H = input.bool(true,  "فعال‌سازی ۴ ساعته", group=grpTf)
InpShow1H = input.bool(true,  "فعال‌سازی ۱ ساعته", group=grpTf)

// ─────────────────────────────────────────────────────────────────────────────
// نمایش
// ─────────────────────────────────────────────────────────────────────────────
grpDisp = "نمایش"
showMS          = input.bool(true,  "نمایش خطوط MS", group=grpDisp)
InpShowMSLabels = input.bool(true,  "لیبل MSH/MSL", group=grpDisp)
showOB          = input.bool(true,  "نمایش Order Block", group=grpDisp)
showRB          = input.bool(true,  "نمایش Rejection Block", group=grpDisp)
showFVG         = input.bool(true,  "نمایش FVG", group=grpDisp)
InpShowRaidSwing= input.bool(false, "Raid روی سوینگ (ضعیف‌تر)", group=grpDisp)
showLP          = input.bool(true,  "نمایش LP", group=grpDisp)
showOrigin      = input.bool(true,  "نمایش Origin", group=grpDisp)
InpShowBreakerBlock = input.bool(true, "نمایش Breaker Block (فقط بصری)", group=grpDisp)
InpShowHiddenOB = input.bool(false, "نمایش Hidden OB (آموزشی، خاموش)", group=grpDisp)
showZones       = input.bool(true,  "نمایش نواحی", group=grpDisp)
showZoneLabels  = input.bool(true,  "لیبل نواحی", group=grpDisp)
showSessions    = input.bool(true,  "نمایش سشن‌ها", group=grpDisp)
showTable       = input.bool(true,  "نمایش جدول وضعیت", group=grpDisp)
cBull      = input.color(color.new(#00c853, 0),  "رنگ صعودی", group=grpDisp)
cBear      = input.color(color.new(#ff5252, 0),  "رنگ نزولی", group=grpDisp)
cMSHigh    = input.color(color.new(#00e676, 0),  "رنگ MS High", group=grpDisp)
cMSLow     = input.color(color.new(#ff5252, 0),  "رنگ MS Low", group=grpDisp)
cRB        = input.color(color.new(#ff9100, 0),  "رنگ Rejection Block", group=grpDisp)
cLP        = input.color(color.new(#40c4ff, 0),  "رنگ LP", group=grpDisp)
cOrigin    = input.color(color.new(#d500f9, 0),  "رنگ Origin", group=grpDisp)
cBreaker   = input.color(color.new(#9e9e9e, 0),  "رنگ Breaker/HOB", group=grpDisp)

// ─────────────────────────────────────────────────────────────────────────────
// سشن‌ها (ساعت نیویورک ثابت UTC-5)
// ─────────────────────────────────────────────────────────────────────────────
grpSess = "سشن‌ها"
InpShowAsia  = input.bool(true, "آسیا (۲۰-۲۴)", group=grpSess)
InpShowLondon= input.bool(true, "کیل‌زون لندن (۰۱-۰۵)", group=grpSess)
InpShowLunch = input.bool(true, "ناهار (۰۵-۰۷)", group=grpSess)
InpShowNY    = input.bool(true, "کیل‌زون نیویورک (۰۷-۱۰)", group=grpSess)
InpShowLC    = input.bool(true, "کلوز لندن (۱۰-۱۲)", group=grpSess)
cAsia   = input.color(color.new(#546e7a, 0), "رنگ آسیا", group=grpSess)
cLondon = input.color(color.new(#1e88e5, 0), "رنگ لندن", group=grpSess)
cLunch  = input.color(color.new(#6d4c41, 0), "رنگ ناهار", group=grpSess)
cNY     = input.color(color.new(#43a047, 0), "رنگ نیویورک", group=grpSess)
cLC     = input.color(color.new(#8e24aa, 0), "رنگ کلوز لندن", group=grpSess)
InpSessionDays = input.int(5, "تعداد روزهای نمایش سشن", minval=1, maxval=30, group=grpSess)

// ─────────────────────────────────────────────────────────────────────────────
// محاسبات و حافظه
// ─────────────────────────────────────────────────────────────────────────────
grpH = "محاسبات"
InpHist                = input.int(800, "طول تاریخچه هر تایم‌فریم (کندل)", minval=200, maxval=2000, group=grpH)
InpNearestZonesCount   = input.int(2, "تعداد نقاط مهم نگه‌داشته بالا/پایین", minval=1, maxval=5, group=grpH)
InpSHBMSMaxCandles     = input.int(4, "حداکثر کندل بین شکار و کلوز نقطه ۳ (shBms)", minval=1, maxval=10, group=grpH)
InpOBMaxConfirmCandles = input.int(2, "حداکثر کندل تأیید سریع Order Block", minval=1, maxval=5, group=grpH)
InpHTFOBMaxUses        = input.int(3, "حداکثر استفاده ۳ نقطه OB در تایم‌فریم بالا", minval=1, maxval=5, group=grpH)

// ─────────────────────────────────────────────────────────────────────────────
// پارامترهای بخش ۱۰ (رفع ابهام‌های سلیقه‌ای)
// ─────────────────────────────────────────────────────────────────────────────
grpParam = "پارامترهای استراتژی (بخش ۱۰)"
InpEntryMethod          = input.string("TokeLP", "روش ورود", options=["TokeLP", "Origin", "Split"], group=grpParam)
InpRiskSplitPercent     = input.float(50.0, "درصد ریسک حالت تقسیمی", minval=1, maxval=99, group=grpParam)
InpMaxSmallLPPips       = input.float(5.0, "آستانه ورود مستقیم (پیپ)", minval=0, group=grpParam)
InpTPMode               = input.string("NextKeyLevel", "حالت حد سود", options=["NextKeyLevel", "FixedRR"], group=grpParam)
InpFixedRR              = input.float(2.0, "نسبت R:R ثابت", minval=0.1, group=grpParam)
InpMaxConcurrentTrades  = input.int(1, "حداکثر معاملات هم‌زمان", minval=1, group=grpParam)
InpAvoidFullMonday      = input.bool(true, "غیرفعال‌سازی کل دوشنبه (نه فقط لندن)", group=grpParam)
InpNewsPreMinutes       = input.int(30, "دقیقه ممنوع قبل خبر", minval=0, maxval=60, group=grpParam)
InpNewsPostMinutes      = input.int(30, "دقیقه ممنوع بعد خبر", minval=0, maxval=60, group=grpParam)
InpCloseOnNewsAt1to1    = input.bool(false, "بستن معامله در ۱:۱ قبل خبر (فاز ۲)", group=grpParam)
InpConfirmTFLookup      = input.bool(true, "فعال بودن هر ۳ گزینه تأییدیه (فاز ۲)", group=grpParam)
InpYearEndBlackoutDays  = input.int(10, "روزهای پایانی سال (بلک‌اوت)", minval=0, maxval=30, group=grpParam)
InpYearStartCutoffDate  = input.int(10, "تاریخ شروع معامله در ابتدای سال", minval=1, maxval=15, group=grpParam)
InpNewsTimes = input.string("", "زمان اخبار پراهمیت — فرمت: YYYY-MM-DD HH:MM (ساعت نیویورک)؛ چند مورد با , جدا کنید", group=grpParam)

// ─────────────────────────────────────────────────────────────────────────────
// مدل داده (بخش ۱۳)
// ─────────────────────────────────────────────────────────────────────────────
type SwingPoint
    float price
    int   t

type Zone
    string kind
    bool   bull
    float  hi
    float  lo
    int    t
    int    sec
    bool   isTested
    int    usesLeft
    float  k1
    float  k2
    float  k3
    bool   hasK3
    int    endT

type OBCandidate
    int    baseT
    float  hi
    float  lo
    float  baseO
    float  baseC
    bool   bull
    int    barsPassed
    bool   confirmed

type Pattern
    int    p1T
    float  p1Price
    int    p2T
    int    slT
    float  slPrice
    bool   bull
    int    age
    bool   complete

type RaidSig
    int    t
    float  level
    bool   bull
    bool   weak

type TFState
    int    sec
    array<float> oo
    array<float> oh
    array<float> ol
    array<float> oc
    array<int>   ot
    array<SwingPoint> sh
    array<SwingPoint> sl
    float  msHigh
    int    msHighT
    float  msLow
    int    msLowT
    int    msDir
    array<Zone> zones
    array<OBCandidate> obCands
    array<Pattern> patterns
    array<Zone> fvg2
    array<RaidSig> raids
    int    lastT
    bool   init

// ─────────────────────────────────────────────────────────────────────────────
// فلگ‌های هشدار
// ─────────────────────────────────────────────────────────────────────────────
var bool g_newOB = false
var bool g_keyTouch = false
var bool g_msBreak = false
var bool g_raid = false
var bool g_shBms = false
var bool g_lp = false
var bool g_kz = false
var bool g_news = false

// ─────────────────────────────────────────────────────────────────────────────
// اشیای رسم و پاک‌سازی
// ─────────────────────────────────────────────────────────────────────────────
var array<box> gBoxes = array.new<box>()
var array<label> gLabels = array.new<label>()
var array<line> gLines = array.new<line>()

f_addBox(int left, float top, int right, float bottom, color bc, color bg) =>
    box b = box.new(left, top, right, bottom, xloc=xloc.bar_time, border_color=bc, border_width=1, bgcolor=bg)
    array.push(gBoxes, b)

f_addLine(int x1, float y, color c, int w, string stl=line.style_solid) =>
    line ln = line.new(x1, y, time, y, xloc=xloc.bar_time, extend=extend.right, color=c, width=w, style=stl)
    array.push(gLines, ln)

f_addLabel(int x, float y, string txt, color c, string stl, string sz=size.small, color tc=color.white) =>
    label lb = label.new(x, y, txt, xloc=xloc.bar_time, style=stl, color=c, textcolor=tc, size=sz)
    array.push(gLabels, lb)

f_cleanup() =>
    if array.size(gBoxes) > 0
        for i = array.size(gBoxes) - 1 to 0 by 1
            box.delete(array.get(gBoxes, i))
    array.clear(gBoxes)
    if array.size(gLabels) > 0
        for i = array.size(gLabels) - 1 to 0 by 1
            label.delete(array.get(gLabels, i))
    array.clear(gLabels)
    if array.size(gLines) > 0
        for i = array.size(gLines) - 1 to 0 by 1
            line.delete(array.get(gLines, i))
    array.clear(gLines)

// ─────────────────────────────────────────────────────────────────────────────
// سشن فعلی (بخش ۳) — بازگشت [نام سشن، مجاز بودن ورود]
// ─────────────────────────────────────────────────────────────────────────────
f_sessionAt(int t) =>
    int nyh = hour(t, "Etc/GMT+5")
    string nm = "خارج از سشن"
    bool allow = false
    if nyh >= 20
        nm := "آسیا"
    else
        if nyh >= 1 and nyh < 5
            nm := "کیل‌زون لندن"
            allow := not (InpAvoidFullMonday and dayofweek(t, "Etc/GMT+5") == dayofweek.monday)
        else
            if nyh >= 5 and nyh < 7
                nm := "ناهار"
            else
                if nyh >= 7 and nyh < 10
                    nm := "کیل‌زون نیویورک"
                    allow := true
                else
                    if nyh >= 10 and nyh < 12
                        nm := "کلوز لندن"
    [nm, allow]

// ─────────────────────────────────────────────────────────────────────────────
// نهایی‌سازی Order Block (شرط قدرت: وجود FVG هم‌جهت در همان حرکت)
// ─────────────────────────────────────────────────────────────────────────────
f_finalizeOB(TFState st, OBCandidate cd, int endT) =>
    bool result = false
    bool strong = false
    if array.size(st.zones) > 0
        for i = 0 to array.size(st.zones) - 1
            Zone z = array.get(st.zones, i)
            if z.kind == "FVG" and z.bull == cd.bull and z.t > cd.baseT and z.t <= endT
                strong := true
                break
    if strong
        float mid = (cd.baseO + cd.baseC) / 2
        float k1 = cd.bull ? cd.hi : cd.baseO
        float k2 = cd.bull ? cd.baseO : cd.lo
        bool htf = st.sec >= 86400
        array.push(st.zones, Zone.new("OB", cd.bull, cd.hi, cd.lo, cd.baseT, st.sec, false, htf ? InpHTFOBMaxUses : 1, k1, k2, mid, htf, endT))
        result := true
    result

// ─────────────────────────────────────────────────────────────────────────────
// تکمیل الگوی shBms → LP + Origin + Breaker (بخش ۵.۹)
// ─────────────────────────────────────────────────────────────────────────────
f_completePattern(TFState st, Pattern p, int n, int t, float o, float h, float l, float c) =>
    bool result = false
    float minL = l
    float maxH = h
    if n > 0
        for i = n - 1 to 0 by 1
            int bt = array.get(st.ot, i)
            if bt < p.p1T
                break
            minL := math.min(minL, array.get(st.ol, i))
            maxH := math.max(maxH, array.get(st.oh, i))
    array.push(st.zones, Zone.new("LP", p.bull, maxH, minL, p.p1T, st.sec, false, 1, 0, 0, 0, false, t))
    result := true
    if n > 0
        for i = n - 1 to 0 by 1
            int bt = array.get(st.ot, i)
            if bt >= p.p2T
                continue
            if p.bull
                if array.get(st.oc, i) < array.get(st.oo, i)
                    array.push(st.zones, Zone.new("Origin", p.bull, array.get(st.oo, i), array.get(st.oc, i), bt, st.sec, false, 1, 0, 0, 0, false, bt))
                    break
            else
                if array.get(st.oc, i) > array.get(st.oo, i)
                    array.push(st.zones, Zone.new("Origin", p.bull, array.get(st.oc, i), array.get(st.oo, i), bt, st.sec, false, 1, 0, 0, 0, false, bt))
                    break
    if n >= 1
        if p.bull
            if array.get(st.oc, n - 1) < array.get(st.oo, n - 1)
                array.push(st.zones, Zone.new("Breaker", p.bull, array.get(st.oo, n - 1), array.get(st.oc, n - 1), array.get(st.ot, n - 1), st.sec, false, 1, 0, 0, 0, false, t))
        else
            if array.get(st.oc, n - 1) > array.get(st.oo, n - 1)
                array.push(st.zones, Zone.new("Breaker", p.bull, array.get(st.oc, n - 1), array.get(st.oo, n - 1), array.get(st.ot, n - 1), st.sec, false, 1, 0, 0, 0, false, t))
    if array.size(st.zones) > 60
        array.shift(st.zones)
    result

// ─────────────────────────────────────────────────────────────────────────────
// تشخیص سوینگ (بخش ۵.۱)
// ─────────────────────────────────────────────────────────────────────────────
f_tfUpdate(TFState st, int t, float o, float h, float l, float c) =>
    bool eOB = false
    bool eTouch = false
    bool eMS = false
    bool eRaid = false
    bool eSh = false
    bool eLP = false
    if not na(t) and st.lastT != t
        st.lastT := t
        array.push(st.oo, o)
        array.push(st.oh, h)
        array.push(st.ol, l)
        array.push(st.oc, c)
        array.push(st.ot, t)
        if array.size(st.oo) > InpHist
            array.shift(st.oo)
            array.shift(st.oh)
            array.shift(st.ol)
            array.shift(st.oc)
            array.shift(st.ot)
        int n = array.size(st.oo) - 1
        bool newSh = false
        bool newSl = false
        if n >= 3
            if array.get(st.oh, n - 2) > array.get(st.oh, n - 3) and array.get(st.oh, n - 2) > array.get(st.oh, n - 1)
                array.push(st.sh, SwingPoint.new(array.get(st.oh, n - 2), array.get(st.ot, n - 2)))
                newSh := true
                if array.size(st.sh) > 200
                    array.shift(st.sh)
            if array.get(st.ol, n - 2) < array.get(st.ol, n - 3) and array.get(st.ol, n - 2) < array.get(st.ol, n - 1)
                array.push(st.sl, SwingPoint.new(array.get(st.ol, n - 2), array.get(st.ot, n - 2)))
                newSl := true
                if array.size(st.sl) > 200
                    array.shift(st.sl)

        // ── مقداردهی اولیه ساختار بازار از گذشته (بخش ۵.۲) ──
        if not st.init and n >= 3
            st.init := true
            if array.size(st.sl) > 0
                SwingPoint s0 = array.get(st.sl, 0)
                st.msLow := s0.price
                st.msLowT := s0.t
                if array.size(st.sh) > 0
                    for i = 0 to array.size(st.sh) - 1
                        SwingPoint s = array.get(st.sh, i)
                        if s.t > s0.t
                            st.msHigh := s.price
                            st.msHighT := s.t
                            st.msDir := 1
                            break

        float msh = st.msHigh
        float msl = st.msLow

        // ── Raid روی MS (بخش ۵.۸) ──
        if not na(msh) and h > msh and c <= msh
            array.push(st.raids, RaidSig.new(t, msh, false, false))
            eRaid := true
        if not na(msl) and l < msl and c >= msl
            array.push(st.raids, RaidSig.new(t, msl, true, false))
            eRaid := true
        if InpShowRaidSwing
            if array.size(st.sh) > 0
                SwingPoint s = array.last(st.sh)
                if h > s.price and c < s.price
                    array.push(st.raids, RaidSig.new(t, s.price, false, true))
                    eRaid := true
            if array.size(st.sl) > 0
                SwingPoint s = array.last(st.sl)
                if l < s.price and c > s.price
                    array.push(st.raids, RaidSig.new(t, s.price, true, true))
                    eRaid := true
        if array.size(st.raids) > 12
            array.shift(st.raids)

        // ── ساختار بازار: شکست/به‌روزرسانی (بخش ۵.۲) ──
        if not na(msh) and c > msh
            st.msHigh := na
            st.msHighT := 0
            st.msDir := 1
            eMS := true
            if array.size(st.sl) > 0
                for i = array.size(st.sl) - 1 to 0 by 1
                    SwingPoint s = array.get(st.sl, i)
                    if s.t < t
                        st.msLow := s.price
                        st.msLowT := s.t
                        break
        if not na(msl) and c < msl
            st.msLow := na
            st.msLowT := 0
            st.msDir := -1
            eMS := true
            if array.size(st.sh) > 0
                for i = array.size(st.sh) - 1 to 0 by 1
                    SwingPoint s = array.get(st.sh, i)
                    if s.t < t
                        st.msHigh := s.price
                        st.msHighT := s.t
                        break
        if na(st.msLow) and newSl
            st.msLow := array.get(st.ol, n - 2)
            st.msLowT := array.get(st.ot, n - 2)
        if na(st.msHigh) and newSh and not na(st.msLow)
            st.msHigh := array.get(st.oh, n - 2)
            st.msHighT := array.get(st.ot, n - 2)
            if st.msDir == 0
                st.msDir := 1

        // ── به‌روزرسانی پر شدن جزئی FVG (بخش ۵.۶) ──
        int zi = 0
        while zi < array.size(st.zones)
            Zone z = array.get(st.zones, zi)
            bool rem = false
            if z.kind == "FVG" and z.t < t
                if z.bull
                    if l <= z.lo
                        if h >= z.hi
                            rem := true
                        else
                            if h > z.lo
                                z.lo := h
                                array.set(st.zones, zi, z)
                    else
                        if h >= z.hi
                            z.hi := l
                            array.set(st.zones, zi, z)
                else
                    if h >= z.hi
                        if l <= z.lo
                            rem := true
                        else
                            z.hi := l
                            array.set(st.zones, zi, z)
                    else
                        if l <= z.lo
                            z.lo := h
                            array.set(st.zones, zi, z)
            if rem
                array.remove(st.zones, zi)
            else
                zi += 1

        // ── FVG نوع ۱ (سه‌کندلی) ──
        if n >= 2
            float midO = array.get(st.oo, n - 1)
            float midC = array.get(st.oc, n - 1)
            bool htfFVG = st.sec >= 86400
            if midC > midO
                float gapLo = array.get(st.oh, n - 2)
                float gapHi = array.get(st.ol, n)
                if gapHi > gapLo
                    array.push(st.zones, Zone.new("FVG", true, gapHi, gapLo, array.get(st.ot, n - 1), st.sec, false, 1, 0, 0, htfFVG ? (gapHi + gapLo) / 2 : 0, htfFVG, 0))
            if midC < midO
                float gapHi = array.get(st.ol, n - 2)
                float gapLo = array.get(st.oh, n)
                if gapHi > gapLo
                    array.push(st.zones, Zone.new("FVG", false, gapHi, gapLo, array.get(st.ot, n - 1), st.sec, false, 1, 0, 0, htfFVG ? (gapHi + gapLo) / 2 : 0, htfFVG, 0))

        // ── FVG نوع ۲ (دوکندلی زنده) ──
        bool htfFVG = st.sec >= 86400
        int pi = 0
        while pi < array.size(st.fvg2)
            Zone p = array.get(st.fvg2, pi)
            bool done = false
            if p.bull
                if l <= p.hi
                    done := true
                else
                    if c > o
                        if p.k1 > p.hi
                            array.push(st.zones, Zone.new("FVG", true, p.k1, p.hi, p.t, st.sec, false, 1, 0, 0, htfFVG ? (p.k1 + p.hi) / 2 : 0, htfFVG, 0))
                        done := true
                    else
                        if c < o
                            done := true
            else
                if h >= p.hi
                    done := true
                else
                    if c < o
                        if p.hi > p.k1
                            array.push(st.zones, Zone.new("FVG", false, p.hi, p.k1, p.t, st.sec, false, 1, 0, 0, htfFVG ? (p.hi + p.k1) / 2 : 0, htfFVG, 0))
                        done := true
                    else
                        if c > o
                            done := true
            if done
                array.remove(st.fvg2, pi)
            else
                pi += 1
        if n >= 2
            float midO = array.get(st.oo, n - 1)
            float midC = array.get(st.oc, n - 1)
            if midC > midO
                array.push(st.fvg2, Zone.new("FVG2", true, array.get(st.oh, n - 2), array.get(st.oh, n - 2), array.get(st.ot, n - 1), st.sec, false, 1, array.get(st.ol, n - 1), 0, 0, false, 0))
            if midC < midO
                array.push(st.fvg2, Zone.new("FVG2", false, array.get(st.ol, n - 2), array.get(st.ol, n - 2), array.get(st.ot, n - 1), st.sec, false, 1, array.get(st.oh, n - 1), 0, 0, false, 0))
            if array.size(st.fvg2) > 20
                array.shift(st.fvg2)

        // ── Order Block (بخش ۵.۴) ──
        if n >= 1
            bool baseUp = array.get(st.oc, n - 1) > array.get(st.oo, n - 1)
            bool baseDn = array.get(st.oc, n - 1) < array.get(st.oo, n - 1)
            if baseUp and c < o
                array.push(st.obCands, OBCandidate.new(array.get(st.ot, n - 1), array.get(st.oh, n - 1), array.get(st.ol, n - 1), array.get(st.oo, n - 1), array.get(st.oc, n - 1), false, 0, false))
            if baseDn and c > o
                array.push(st.obCands, OBCandidate.new(array.get(st.ot, n - 1), array.get(st.oh, n - 1), array.get(st.ol, n - 1), array.get(st.oo, n - 1), array.get(st.oc, n - 1), true, 0, false))
            if array.size(st.obCands) > 30
                array.shift(st.obCands)
        int ci = 0
        while ci < array.size(st.obCands)
            OBCandidate cd = array.get(st.obCands, ci)
            cd.barsPassed += 1
            bool remove = false
            if not cd.confirmed and cd.barsPassed > InpOBMaxConfirmCandles
                remove := true
            if not remove and not cd.confirmed
                if cd.bull
                    if c > cd.hi
                        cd.confirmed := true
                    else
                        if c < o
                            remove := true
                else
                    if c < cd.lo
                        cd.confirmed := true
                    else
                        if c > o
                            remove := true
            if not remove and cd.confirmed
                if cd.bull
                    if c > o
                        if h > cd.hi
                            cd.hi := h
                    else
                        if f_finalizeOB(st, cd, t)
                            eOB := true
                        remove := true
                else
                    if c < o
                        if l < cd.lo
                            cd.lo := l
                    else
                        if f_finalizeOB(st, cd, t)
                            eOB := true
                        remove := true
            if remove
                array.remove(st.obCands, ci)
            else
                array.set(st.obCands, ci, cd)
                ci += 1

        // ── Rejection Block (بخش ۵.۵، فقط روزانه به بالا) ──
        if st.sec >= 86400 and n >= 3
            if newSh
                float piv = array.get(st.oh, n - 2)
                float oo = array.get(st.oo, n - 2)
                float cc = array.get(st.oc, n - 2)
                float lo = math.abs(piv - oo) < math.abs(piv - cc) ? oo : cc
                if lo < piv
                    array.push(st.zones, Zone.new("RB", false, piv, lo, array.get(st.ot, n - 2), st.sec, false, 1, 0, 0, 0, false, array.get(st.ot, n - 2)))
            if newSl
                float piv = array.get(st.ol, n - 2)
                float oo = array.get(st.oo, n - 2)
                float cc = array.get(st.oc, n - 2)
                float hi = math.abs(piv - oo) < math.abs(piv - cc) ? oo : cc
                if hi > piv
                    array.push(st.zones, Zone.new("RB", true, hi, piv, array.get(st.ot, n - 2), st.sec, false, 1, 0, 0, 0, false, array.get(st.ot, n - 2)))

        // ── الگوی shBms: شکار (نقطه ۲) ──
        if array.size(st.sh) > 0 and array.size(st.sl) > 0
            SwingPoint s = array.last(st.sh)
            if h > s.price and c < s.price
                for i = array.size(st.sl) - 1 to 0 by 1
                    SwingPoint sl = array.get(st.sl, i)
                    if sl.t > s.t and sl.t < t
                        array.push(st.patterns, Pattern.new(s.t, s.price, t, sl.t, sl.price, false, 0, false))
                        array.remove(st.sh, array.size(st.sh) - 1)
                        break
        if array.size(st.sl) > 0 and array.size(st.sh) > 0
            SwingPoint s = array.last(st.sl)
            if l < s.price and c > s.price
                for i = array.size(st.sh) - 1 to 0 by 1
                    SwingPoint sh2 = array.get(st.sh, i)
                    if sh2.t > s.t and sh2.t < t
                        array.push(st.patterns, Pattern.new(s.t, s.price, t, sh2.t, sh2.price, true, 0, false))
                        array.remove(st.sl, array.size(st.sl) - 1)
                        break
        if array.size(st.patterns) > 10
            array.shift(st.patterns)

        // ── الگوی shBms: کلوز نقطه ۳ (بخش ۵.۹) ──
        int k = 0
        while k < array.size(st.patterns)
            Pattern p = array.get(st.patterns, k)
            p.age += 1
            bool done = false
            if p.age > InpSHBMSMaxCandles
                done := true
            else
                if p.bull
                    if c > p.slPrice
                        if f_completePattern(st, p, n, t, o, h, l, c)
                            eSh := true
                            eLP := true
                        done := true
                else
                    if c < p.slPrice
                        if f_completePattern(st, p, n, t, o, h, l, c)
                            eSh := true
                            eLP := true
                        done := true
            if done
                array.remove(st.patterns, k)
            else
                array.set(st.patterns, k, p)
                k += 1

        // ── لمس/مصرف نواحی یک‌بارمصرف (بخش ۵.۴، ۵.۵، گام ۴) ──
        zi := 0
        while zi < array.size(st.zones)
            Zone z = array.get(st.zones, zi)
            bool rem = false
            if (z.kind == "OB" or z.kind == "RB") and t > z.endT
                if h >= z.lo and l <= z.hi
                    if z.usesLeft > 0
                        z.usesLeft -= 1
                        eTouch := true
                    if z.kind == "RB"
                        rem := true
                    else
                        if z.sec < 86400
                            rem := true
                        else
                            if z.usesLeft <= 0
                                rem := true
            if not z.isTested and z.kind == "LP"
                if z.bull ? c > z.hi : c < z.lo
                    z.isTested := true
            if not z.isTested and z.kind == "Origin"
                if z.bull ? c > z.hi : c < z.lo
                    z.isTested := true
            if rem
                array.remove(st.zones, zi)
            else
                array.set(st.zones, zi, z)
                zi += 1

        // ── Hidden OB (بخش ۵.۷، فقط آموزشی) ──
        if InpShowHiddenOB and n >= 2
            bool above = false
            bool below = false
            if array.size(st.zones) > 0
                for i = 0 to array.size(st.zones) - 1
                    Zone z = array.get(st.zones, i)
                    if z.kind == "FVG"
                        if z.lo >= array.get(st.oh, n - 1)
                            above := true
                        if z.hi <= array.get(st.ol, n - 1)
                            below := true
            if above and below
                array.push(st.zones, Zone.new("HOB", false, array.get(st.oh, n - 1), array.get(st.ol, n - 1), array.get(st.ot, n - 1), st.sec, false, 1, 0, 0, 0, false, t))
        if array.size(st.zones) > 60
            array.shift(st.zones)
    [eOB, eTouch, eMS, eRaid, eSh, eLP]

// ─────────────────────────────────────────────────────────────────────────────
// متن لیبل ناحیه
// ─────────────────────────────────────────────────────────────────────────────
f_zoneLabel(Zone z) =>
    string s = z.kind == "OB" ? "OB" : z.kind == "RB" ? "RB" : z.kind == "FVG" ? (z.bull ? "BC" : "CV") : z.kind == "LP" ? "LP" : z.kind == "Origin" ? "Origin" : z.kind == "Breaker" ? "Breaker" : "HOB"
    s

// ─────────────────────────────────────────────────────────────────────────────
// رسم یک تایم‌فریم (فقط روی آخرین کندل)
// ─────────────────────────────────────────────────────────────────────────────
f_drawTF(TFState st, string lab) =>
    int tfSec = timeframe.in_seconds(timeframe.period)
    if showMS and st.sec == tfSec
        if not na(st.msHigh)
            f_addLine(st.msHighT, st.msHigh, cMSHigh, 1)
            if InpShowMSLabels
                f_addLabel(time, st.msHigh, lab + " MSH", color.new(cMSHigh, 15), label.style_label_down)
        if not na(st.msLow)
            f_addLine(st.msLowT, st.msLow, cMSLow, 1)
            if InpShowMSLabels
                f_addLabel(time, st.msLow, lab + " MSL", color.new(cMSLow, 15), label.style_label_up)
    if showZones
        int win = 600 * st.sec * 1000
        if array.size(st.zones) > 0
            int nb1 = -1, nb2 = -1, na1 = -1, na2 = -1
            float db1 = na
            float db2 = na
            float da1 = na
            float da2 = na
            float px = close
            for i = 0 to array.size(st.zones) - 1
                Zone z = array.get(st.zones, i)
                if z.isTested and z.usesLeft <= 0
                    continue
                if z.sec < tfSec
                    continue
                if z.t < time - win
                    continue
                bool showThis = (z.kind == "OB" and showOB) or (z.kind == "RB" and showRB) or (z.kind == "FVG" and showFVG) or (z.kind == "LP" and showLP) or (z.kind == "Origin" and showOrigin) or (z.kind == "Breaker" and InpShowBreakerBlock) or (z.kind == "HOB" and InpShowHiddenOB)
                if not showThis
                    continue
                if z.hi < px
                    float d = px - z.hi
                    if na(db1) or d < db1
                        nb2 := nb1
                        db2 := db1
                        nb1 := i
                        db1 := d
                    else
                        if na(db2) or d < db2
                            nb2 := i
                            db2 := d
                else
                    if z.lo > px
                        float d = z.lo - px
                        if na(da1) or d < da1
                            na2 := na1
                            da2 := da1
                            na1 := i
                            da1 := d
                        else
                            if na(da2) or d < da2
                                na2 := i
                                da2 := d
            for i = 0 to array.size(st.zones) - 1
                if i != nb1 and i != nb2 and i != na1 and i != na2
                    continue
                Zone z = array.get(st.zones, i)
                color bc = z.bull ? cBull : cBear
                if z.kind == "RB"
                    bc := cRB
                if z.kind == "LP"
                    bc := cLP
                if z.kind == "Origin"
                    bc := cOrigin
                if z.kind == "Breaker" or z.kind == "HOB"
                    bc := cBreaker
                if z.kind == "OB" and z.hasK3
                    f_addLine(z.t, z.k1, bc, 1)
                    f_addLine(z.t, z.k2, bc, 1)
                    f_addLine(z.t, z.k3, bc, 1)
                    if showZoneLabels
                        f_addLabel(time, z.k3, lab + " MTH", color.new(cLP, 10), label.style_label_down, size.tiny)
                else
                    if z.kind == "FVG" and z.hasK3
                        f_addLine(z.t, z.hi, bc, 1)
                        f_addLine(z.t, z.lo, bc, 1)
                        f_addLine(z.t, (z.hi + z.lo) / 2, bc, 1)
                        if showZoneLabels
                            f_addLabel(time, (z.hi + z.lo) / 2, lab + " " + f_zoneLabel(z), color.new(bc, 12), z.bull ? label.style_label_down : label.style_label_up)
                    else
                        f_addBox(z.t, z.hi, time, z.lo, bc, color.new(bc, 87))
                        if showZoneLabels
                            f_addLabel(time, z.bull ? z.lo : z.hi, lab + " " + f_zoneLabel(z), color.new(bc, 12), z.bull ? label.style_label_down : label.style_label_up)

// ─────────────────────────────────────────────────────────────────────────────
// جعبه‌های سشن (بخش ۳)
// ─────────────────────────────────────────────────────────────────────────────
f_drawSessions() =>
    int tfSec = timeframe.in_seconds(timeframe.period)
    float top = math.max(high, close)
    float bot = math.min(low, close)
    if tfSec <= 14400
        int dayT = time
        int utc = timestamp("UTC", year(dayT, "UTC"), month(dayT, "UTC"), dayofmonth(dayT, "UTC"), 0, 0)
        if InpShowAsia
            f_addBox(utc + 25 * 3600000, top, utc + 29 * 3600000, bot, cAsia, color.new(cAsia, 90))
        if InpShowLondon
            f_addBox(utc + 6 * 3600000, top, utc + 10 * 3600000, bot, cLondon, color.new(cLondon, 90))
        if InpShowLunch
            f_addBox(utc + 10 * 3600000, top, utc + 12 * 3600000, bot, cLunch, color.new(cLunch, 90))
        if InpShowNY
            f_addBox(utc + 12 * 3600000, top, utc + 15 * 3600000, bot, cNY, color.new(cNY, 90))
        if InpShowLC
            f_addBox(utc + 15 * 3600000, top, utc + 17 * 3600000, bot, cLC, color.new(cLC, 90))

// ─────────────────────────────────────────────────────────────────────────────
// حالت‌های هر تایم‌فریم
// ─────────────────────────────────────────────────────────────────────────────
var TFState stM = TFState.new(timeframe.in_seconds(InpTF_M), array.new_float(), array.new_float(), array.new_float(), array.new_float(), array.new_int(), array.new<SwingPoint>(), array.new<SwingPoint>(), na, 0, na, 0, 0, array.new<Zone>(), array.new<OBCandidate>(), array.new<Pattern>(), array.new<Zone>(), array.new<RaidSig>(), 0, false)
var TFState stW = TFState.new(timeframe.in_seconds(InpTF_W), array.new_float(), array.new_float(), array.new_float(), array.new_float(), array.new_int(), array.new<SwingPoint>(), array.new<SwingPoint>(), na, 0, na, 0, 0, array.new<Zone>(), array.new<OBCandidate>(), array.new<Pattern>(), array.new<Zone>(), array.new<RaidSig>(), 0, false)
var TFState stD = TFState.new(timeframe.in_seconds(InpTF_D), array.new_float(), array.new_float(), array.new_float(), array.new_float(), array.new_int(), array.new<SwingPoint>(), array.new<SwingPoint>(), na, 0, na, 0, 0, array.new<Zone>(), array.new<OBCandidate>(), array.new<Pattern>(), array.new<Zone>(), array.new<RaidSig>(), 0, false)
var TFState st4 = TFState.new(timeframe.in_seconds(InpTF_4H), array.new_float(), array.new_float(), array.new_float(), array.new_float(), array.new_int(), array.new<SwingPoint>(), array.new<SwingPoint>(), na, 0, na, 0, 0, array.new<Zone>(), array.new<OBCandidate>(), array.new<Pattern>(), array.new<Zone>(), array.new<RaidSig>(), 0, false)
var TFState st1 = TFState.new(timeframe.in_seconds(InpTF_1H), array.new_float(), array.new_float(), array.new_float(), array.new_float(), array.new_int(), array.new<SwingPoint>(), array.new<SwingPoint>(), na, 0, na, 0, 0, array.new<Zone>(), array.new<OBCandidate>(), array.new<Pattern>(), array.new<Zone>(), array.new<RaidSig>(), 0, false)

// ─────────────────────────────────────────────────────────────────────────────
// جدول وضعیت (بند ۴ بخش ۱۱)
// ─────────────────────────────────────────────────────────────────────────────
f_trendText(TFState st) =>
    string s = st.msDir == 1 ? "صعودی" : st.msDir == -1 ? "نزولی" : "رنج/نامشخص"
    s

f_trendColor(TFState st) =>
    color c = st.msDir == 1 ? #00c853 : st.msDir == -1 ? #ff5252 : color.gray
    c

f_drawTable(string nm, bool allow, bool mon, bool blackout, bool inNews, bool nearNews) =>
    var table tb = table.new(position.top_right, 2, 11, bgcolor=color.new(#0d1117, 5), border_width=1, frame_color=color.new(color.gray, 70))
    table.cell(tb, 0, 0, "وضعیت ICT (فاز ۱)", text_color=color.white, bgcolor=color.new(#1f2430, 0), text_size=size.small)
    table.cell(tb, 1, 0, "", bgcolor=color.new(#1f2430, 0))
    table.cell(tb, 0, 1, "ماهانه", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 1, f_trendText(stM), text_color=f_trendColor(stM), text_size=size.small)
    table.cell(tb, 0, 2, "هفتگی", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 2, f_trendText(stW), text_color=f_trendColor(stW), text_size=size.small)
    table.cell(tb, 0, 3, "روزانه", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 3, f_trendText(stD), text_color=f_trendColor(stD), text_size=size.small)
    table.cell(tb, 0, 4, "۴ ساعته", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 4, f_trendText(st4), text_color=f_trendColor(st4), text_size=size.small)
    table.cell(tb, 0, 5, "۱ ساعته", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 5, f_trendText(st1), text_color=f_trendColor(st1), text_size=size.small)
    table.cell(tb, 0, 6, "سشن فعلی", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 6, nm + (allow ? " — مجاز" : " — ممنوع"), text_color=allow ? #00c853 : #ff5252, text_size=size.small)
    table.cell(tb, 0, 7, "دوشنبه", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 7, mon ? "بله (غیرفعال)" : "خیر", text_color=mon ? #ff9800 : color.gray, text_size=size.small)
    table.cell(tb, 0, 8, "بازه خبر", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 8, inNews ? "داخل بازه" : nearNews ? "نزدیک" : "عادی", text_color=inNews ? #ff5252 : nearNews ? #ff9800 : color.gray, text_size=size.small)
    table.cell(tb, 0, 9, "بلک‌اوت سال", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 9, blackout ? "فعال" : "غیرفعال", text_color=blackout ? #ff5252 : color.gray, text_size=size.small)
    table.cell(tb, 0, 10, "کیل‌زون مجاز", text_color=color.gray, text_size=size.small)
    table.cell(tb, 1, 10, allow ? "ورود مجاز" : "ورود ممنوع", text_color=allow ? #00c853 : #ff5252, text_size=size.small)

// ─────────────────────────────────────────────────────────────────────────────
// بازنشانی فلگ‌ها + داده چندتایم‌فریمی
// ─────────────────────────────────────────────────────────────────────────────
g_newOB := false
g_keyTouch := false
g_msBreak := false
g_raid := false
g_shBms := false
g_lp := false

mT = request.security(syminfo.tickerid, InpTF_M, time, lookahead=barmerge.lookahead_off)
mO = request.security(syminfo.tickerid, InpTF_M, open, lookahead=barmerge.lookahead_off)
mH = request.security(syminfo.tickerid, InpTF_M, high, lookahead=barmerge.lookahead_off)
mL = request.security(syminfo.tickerid, InpTF_M, low, lookahead=barmerge.lookahead_off)
mC = request.security(syminfo.tickerid, InpTF_M, close, lookahead=barmerge.lookahead_off)
wT = request.security(syminfo.tickerid, InpTF_W, time, lookahead=barmerge.lookahead_off)
wO = request.security(syminfo.tickerid, InpTF_W, open, lookahead=barmerge.lookahead_off)
wH = request.security(syminfo.tickerid, InpTF_W, high, lookahead=barmerge.lookahead_off)
wL = request.security(syminfo.tickerid, InpTF_W, low, lookahead=barmerge.lookahead_off)
wC = request.security(syminfo.tickerid, InpTF_W, close, lookahead=barmerge.lookahead_off)
dT = request.security(syminfo.tickerid, InpTF_D, time, lookahead=barmerge.lookahead_off)
dO = request.security(syminfo.tickerid, InpTF_D, open, lookahead=barmerge.lookahead_off)
dH = request.security(syminfo.tickerid, InpTF_D, high, lookahead=barmerge.lookahead_off)
dL = request.security(syminfo.tickerid, InpTF_D, low, lookahead=barmerge.lookahead_off)
dC = request.security(syminfo.tickerid, InpTF_D, close, lookahead=barmerge.lookahead_off)
h4T = request.security(syminfo.tickerid, InpTF_4H, time, lookahead=barmerge.lookahead_off)
h4O = request.security(syminfo.tickerid, InpTF_4H, open, lookahead=barmerge.lookahead_off)
h4H = request.security(syminfo.tickerid, InpTF_4H, high, lookahead=barmerge.lookahead_off)
h4L = request.security(syminfo.tickerid, InpTF_4H, low, lookahead=barmerge.lookahead_off)
h4C = request.security(syminfo.tickerid, InpTF_4H, close, lookahead=barmerge.lookahead_off)
h1T = request.security(syminfo.tickerid, InpTF_1H, time, lookahead=barmerge.lookahead_off)
h1O = request.security(syminfo.tickerid, InpTF_1H, open, lookahead=barmerge.lookahead_off)
h1H = request.security(syminfo.tickerid, InpTF_1H, high, lookahead=barmerge.lookahead_off)
h1L = request.security(syminfo.tickerid, InpTF_1H, low, lookahead=barmerge.lookahead_off)
h1C = request.security(syminfo.tickerid, InpTF_1H, close, lookahead=barmerge.lookahead_off)

if InpShowM
    [eOb, eTouch, eMs, eRaid, eSh, eLp] = f_tfUpdate(stM, mT, mO, mH, mL, mC)
    g_newOB := eOb or g_newOB
    g_keyTouch := eTouch or g_keyTouch
    g_msBreak := eMs or g_msBreak
    g_raid := eRaid or g_raid
    g_shBms := eSh or g_shBms
    g_lp := eLp or g_lp
if InpShowW
    [eOb, eTouch, eMs, eRaid, eSh, eLp] = f_tfUpdate(stW, wT, wO, wH, wL, wC)
    g_newOB := eOb or g_newOB
    g_keyTouch := eTouch or g_keyTouch
    g_msBreak := eMs or g_msBreak
    g_raid := eRaid or g_raid
    g_shBms := eSh or g_shBms
    g_lp := eLp or g_lp
if InpShowD
    [eOb, eTouch, eMs, eRaid, eSh, eLp] = f_tfUpdate(stD, dT, dO, dH, dL, dC)
    g_newOB := eOb or g_newOB
    g_keyTouch := eTouch or g_keyTouch
    g_msBreak := eMs or g_msBreak
    g_raid := eRaid or g_raid
    g_shBms := eSh or g_shBms
    g_lp := eLp or g_lp
if InpShow4H
    [eOb, eTouch, eMs, eRaid, eSh, eLp] = f_tfUpdate(st4, h4T, h4O, h4H, h4L, h4C)
    g_newOB := eOb or g_newOB
    g_keyTouch := eTouch or g_keyTouch
    g_msBreak := eMs or g_msBreak
    g_raid := eRaid or g_raid
    g_shBms := eSh or g_shBms
    g_lp := eLp or g_lp
if InpShow1H
    [eOb, eTouch, eMs, eRaid, eSh, eLp] = f_tfUpdate(st1, h1T, h1O, h1H, h1L, h1C)
    g_newOB := eOb or g_newOB
    g_keyTouch := eTouch or g_keyTouch
    g_msBreak := eMs or g_msBreak
    g_raid := eRaid or g_raid
    g_shBms := eSh or g_shBms
    g_lp := eLp or g_lp

// ─────────────────────────────────────────────────────────────────────────────
// اخبار پراهمیت (بخش ۴) — ورودی دستی، ساعت نیویورک
// ─────────────────────────────────────────────────────────────────────────────
var array<float> newsT = array.new_float()
var bool newsParsed = false
if not newsParsed
    newsParsed := true
    if str.length(InpNewsTimes) > 0
        array<string> parts = str.split(InpNewsTimes, ",")
        if array.size(parts) > 0
            for i = 0 to array.size(parts) - 1
                string s = str.trim(array.get(parts, i))
                array<string> dt = str.split(s, " ")
                if array.size(dt) < 2
                    continue
                array<string> ds = str.split(array.get(dt, 0), "-")
                array<string> ts = str.split(array.get(dt, 1), ":")
                if array.size(ds) < 3 or array.size(ts) < 2
                    continue
                float y = str.tonumber(array.get(ds, 0))
                float mo = str.tonumber(array.get(ds, 1))
                float da = str.tonumber(array.get(ds, 2))
                float hh = str.tonumber(array.get(ts, 0))
                float mi = str.tonumber(array.get(ts, 1))
                if na(y) or na(mo) or na(da) or na(hh) or na(mi)
                    continue
                array.push(newsT, timestamp("Etc/GMT+5", int(y), int(mo), int(da), int(hh), int(mi)))

bool inNews = false
bool nearNews = false
if array.size(newsT) > 0
    for i = 0 to array.size(newsT) - 1
        float tNews = array.get(newsT, i)
        float w1 = tNews - InpNewsPreMinutes * 60000
        float w2 = tNews + InpNewsPostMinutes * 60000
        if time >= w1 and time <= w2
            inNews := true
        if time >= w1 - 1800000 and time < w1
            nearNews := true
var bool prevNear = false
var bool prevIn = false
if (nearNews and not prevNear) or (inNews and not prevIn)
    g_news := true
prevNear := nearNews
prevIn := inNews

// ─────────────────────────────────────────────────────────────────────────────
// وضعیت فعلی: سشن، روز، بلک‌اوت
// ─────────────────────────────────────────────────────────────────────────────
[nmCur, allowCur] = f_sessionAt(time)
var bool prevAllowed = false
if allowCur != prevAllowed
    g_kz := true
prevAllowed := allowCur

bool mon = dayofweek(time, "Etc/GMT+5") == dayofweek.monday
bool yEnd = month(time, "Etc/GMT+5") == 12 and dayofmonth(time, "Etc/GMT+5") >= 32 - InpYearEndBlackoutDays
bool yStart = month(time, "Etc/GMT+5") == 1 and dayofmonth(time, "Etc/GMT+5") <= InpYearStartCutoffDate
bool blackout = yEnd or yStart

// ─────────────────────────────────────────────────────────────────────────────
// رسم
// ─────────────────────────────────────────────────────────────────────────────
if barstate.islast
    f_cleanup()
    if InpShowM
        f_drawTF(stM, "Monthly")
    if InpShowW
        f_drawTF(stW, "Weekly")
    if InpShowD
        f_drawTF(stD, "Daily")
    if InpShow4H
        f_drawTF(st4, "4H")
    if InpShow1H
        f_drawTF(st1, "1H")
    if showSessions
        f_drawSessions()
    if showTable
        f_drawTable(nmCur, allowCur, mon, blackout, inNews, nearNews)

// ─────────────────────────────────────────────────────────────────────────────
// هشدارها (بند ۵ بخش ۱۱)
// ─────────────────────────────────────────────────────────────────────────────
alertcondition(g_newOB, "تشکیل Order Block جدید", "Order Block معتبر جدیدی تشکیل شد")
alertcondition(g_keyTouch, "لمس نقطه مهم", "قیمت یک Order Block / Rejection Block را لمس کرد")
alertcondition(g_msBreak, "شکست ساختار بازار", "کلوز بالای MS High یا پایین MS Low ثبت شد")
alertcondition(g_raid, "تشخیص Raid", "ریِد روی ساختار بازار تشخیص داده شد")
alertcondition(g_shBms, "تکمیل الگوی shBms", "الگوی Stop-Hunt + Break of Market Structure کامل شد")
alertcondition(g_lp, "تشکیل LP", "ناحیه Launch Pad (LP) تشکیل شد")
alertcondition(g_kz, "تغییر وضعیت کیل‌زون", "وارد/خارج از کیل‌زون مجاز شدید")
alertcondition(g_news, "نزدیک شدن به خبر", "به بازه ممنوعه خبر نزدیک می‌شویم")
