# Fitness-import { useState, useEffect } from "react";
import {
  LineChart, Line, AreaChart, Area, BarChart, Bar,
  XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer
} from "recharts";

// ─── Mock Data ──────────────────────────────────────────────
const MEMBER = {
  name: "สุชาติ วงศ์สกุล",
  nickname: "ชาติ",
  phone: "081-234-5678",
  avatar: null,
  membershipId: "FS-2024-0142",
  package: "รายเดือน Premium",
  packageColor: "#10B981",
  startDate: "1 มี.ค. 2568",
  endDate: "31 พ.ค. 2568",
  daysRemaining: 14,
  qrCode: "FS-2024-0142-QR",
  coachName: "สมชาย พลายงาม",
};

const HEALTH_DATA = [
  { month: "ม.ค.", weight: 82, bodyFat: 28.5, bmi: 27.1, muscle: 58 },
  { month: "ก.พ.", weight: 80, bodyFat: 27.2, bmi: 26.4, muscle: 59 },
  { month: "มี.ค.", weight: 78.5, bodyFat: 26.0, bmi: 25.9, muscle: 60.5 },
  { month: "เม.ย.", weight: 77, bodyFat: 24.8, bmi: 25.4, muscle: 61.8 },
  { month: "พ.ค.", weight: 75.5, bodyFat: 23.5, bmi: 24.9, muscle: 63 },
];

const LATEST_ASSESSMENT = {
  weight: 75.5, height: 174, bmi: 24.9,
  bodyFat: 23.5, muscle: 63, visceralFat: 8,
  bp: "118/76", heartRate: 68,
  handgripLeft: 42, handgripRight: 44,
  tug: 9.2, date: "15 พ.ค. 2568"
};

const CHECKINS = [
  { date: "พฤ 15 พ.ค.", time: "07:24 น.", duration: "62 นาที" },
  { date: "อ 13 พ.ค.", time: "07:10 น.", duration: "55 นาที" },
  { date: "จ 12 พ.ค.", time: "18:45 น.", duration: "70 นาที" },
  { date: "ศ 9 พ.ค.", time: "07:30 น.", duration: "50 นาที" },
  { date: "พ 7 พ.ค.", time: "07:15 น.", duration: "65 นาที" },
];

const WEEKLY_CHECKINS = [
  { week: "W1", days: 3 }, { week: "W2", days: 4 },
  { week: "W3", days: 3 }, { week: "W4", days: 5 },
  { week: "W5", days: 4 },
];

const PROGRAM = {
  name: "โปรแกรมลดไขมัน + สร้างกล้ามเนื้อ",
  daysPerWeek: 4,
  days: [
    {
      day: 1, label: "จันทร์ — ขา + สะโพก", exercises: [
        { name: "Squat", sets: 4, reps: "12", weight: "60 กก." },
        { name: "Leg Press", sets: 3, reps: "15", weight: "80 กก." },
        { name: "Romanian Deadlift", sets: 3, reps: "10", weight: "50 กก." },
        { name: "Calf Raise", sets: 3, reps: "20", weight: "40 กก." },
      ]
    },
    {
      day: 2, label: "พุธ — อก + ไหล่", exercises: [
        { name: "Bench Press", sets: 4, reps: "10", weight: "50 กก." },
        { name: "Incline DB Press", sets: 3, reps: "12", weight: "18 กก." },
        { name: "Shoulder Press", sets: 3, reps: "12", weight: "16 กก." },
        { name: "Lateral Raise", sets: 3, reps: "15", weight: "8 กก." },
      ]
    },
  ]
};

const ADMIN_STATS = {
  totalMembers: 187,
  activeMembers: 142,
  newThisMonth: 23,
  expiringSoon: 18,
  monthlyRevenue: 186430,
  yearlyTarget: 2400000,
};

const REVENUE_DATA = [
  { month: "ม.ค.", revenue: 152000 },
  { month: "ก.พ.", revenue: 168000 },
  { month: "มี.ค.", revenue: 175000 },
  { month: "เม.ย.", revenue: 163000 },
  { month: "พ.ค.", revenue: 186430 },
];

const MEMBERS_LIST = [
  { name: "สมหญิง ดีงาม", phone: "089-xxx-1234", package: "รายเดือน Premium", expiry: "31 พ.ค.", status: "expiring", daysLeft: 14 },
  { name: "วิชัย ใจดี", phone: "081-xxx-5678", package: "รายปี", expiry: "12 ธ.ค.", status: "active", daysLeft: 209 },
  { name: "มาลี สวยงาม", phone: "092-xxx-9012", package: "รายเดือน Basic", expiry: "5 มิ.ย.", status: "active", daysLeft: 19 },
  { name: "ประสิทธิ์ มั่นคง", phone: "086-xxx-3456", package: "รายไตรมาส", expiry: "28 พ.ค.", status: "expiring", daysLeft: 11 },
  { name: "นภา แสงดาว", phone: "091-xxx-7890", package: "รายปี", expiry: "3 มี.ค.", status: "expired", daysLeft: -74 },
];

// ─── Color palette ───────────────────────────────────────────
const C = {
  primary: "#059669",
  primaryLight: "#D1FAE5",
  primaryDark: "#047857",
  bg: "#F8F7F4",
  card: "#FFFFFF",
  text: "#1C1917",
  textMuted: "#78716C",
  border: "#E7E5E4",
  warning: "#D97706",
  danger: "#DC2626",
  dangerLight: "#FEE2E2",
  warningLight: "#FEF3C7",
};

// ─── Tiny Components ─────────────────────────────────────────
const Card = ({ children, className = "" }) => (
  <div className={`bg-white rounded-2xl border border-stone-100 shadow-sm ${className}`}>
    {children}
  </div>
);

const Badge = ({ children, color = "green" }) => {
  const colors = {
    green: "bg-emerald-100 text-emerald-700",
    yellow: "bg-amber-100 text-amber-700",
    red: "bg-red-100 text-red-600",
    gray: "bg-stone-100 text-stone-600",
    blue: "bg-blue-100 text-blue-700",
  };
  return (
    <span className={`text-xs font-medium px-2.5 py-1 rounded-full ${colors[color]}`}>
      {children}
    </span>
  );
};

const MetricCard = ({ label, value, unit, trend, color = "#059669" }) => (
  <Card className="p-4">
    <p className="text-xs text-stone-500 mb-1">{label}</p>
    <div className="flex items-end gap-1">
      <span className="text-2xl font-bold" style={{ color }}>{value}</span>
      {unit && <span className="text-sm text-stone-400 mb-0.5">{unit}</span>}
    </div>
    {trend && (
      <p className={`text-xs mt-1 ${trend > 0 ? "text-red-500" : "text-emerald-600"}`}>
        {trend > 0 ? "▲" : "▼"} {Math.abs(trend)} จากครั้งที่แล้ว
      </p>
    )}
  </Card>
);

// ─── QR Card Component ───────────────────────────────────────
const QRMemberCard = ({ member }) => {
  const [flipped, setFlipped] = useState(false);
  return (
    <div
      className="cursor-pointer select-none"
      onClick={() => setFlipped(!flipped)}
      style={{ perspective: 800 }}
    >
      <div
        style={{
          transition: "transform 0.5s",
          transformStyle: "preserve-3d",
          transform: flipped ? "rotateY(180deg)" : "rotateY(0deg)",
          position: "relative", height: 200,
        }}
      >
        {/* Front */}
        <div
          style={{
            backfaceVisibility: "hidden",
            position: "absolute", inset: 0,
            background: "linear-gradient(135deg, #047857 0%, #059669 60%, #10B981 100%)",
            borderRadius: 20, padding: 24, color: "white",
            display: "flex", flexDirection: "column", justifyContent: "space-between",
          }}
        >
          <div className="flex justify-between items-start">
            <div>
              <p style={{ fontSize: 11, opacity: 0.8, marginBottom: 4 }}>FitStudio Pro</p>
              <p style={{ fontSize: 20, fontWeight: 700 }}>{member.nickname || member.name.split(" ")[0]}</p>
              <p style={{ fontSize: 13, opacity: 0.85 }}>{member.name}</p>
            </div>
            <div style={{
              width: 48, height: 48, background: "rgba(255,255,255,0.2)",
              borderRadius: 12, display: "flex", alignItems: "center", justifyContent: "center",
              fontSize: 22
            }}>💪</div>
          </div>
          <div className="flex justify-between items-end">
            <div>
              <p style={{ fontSize: 11, opacity: 0.7 }}>แพ็กเกจ</p>
              <p style={{ fontSize: 13, fontWeight: 600 }}>{member.package}</p>
            </div>
            <div style={{ textAlign: "right" }}>
              <p style={{ fontSize: 11, opacity: 0.7 }}>หมดอายุ</p>
              <p style={{ fontSize: 13, fontWeight: 600 }}>{member.endDate}</p>
            </div>
          </div>
          <p style={{ fontSize: 10, opacity: 0.6, textAlign: "center", marginTop: 4 }}>
            แตะเพื่อดู QR Code
          </p>
        </div>
        {/* Back (QR) */}
        <div
          style={{
            backfaceVisibility: "hidden",
            transform: "rotateY(180deg)",
            position: "absolute", inset: 0,
            background: "white",
            borderRadius: 20, padding: 16,
            display: "flex", flexDirection: "column", alignItems: "center", justifyContent: "center",
            border: "2px solid #D1FAE5",
          }}
        >
          <div style={{
            width: 120, height: 120,
            background: "#F8F7F4",
            borderRadius: 12,
            display: "grid",
            gridTemplateColumns: "repeat(7, 1fr)",
            gap: 2, padding: 8,
          }}>
            {Array.from({ length: 49 }).map((_, i) => (
              <div key={i} style={{
                borderRadius: 1,
                background: Math.random() > 0.5 ? "#047857" : "transparent",
                aspectRatio: 1
              }} />
            ))}
          </div>
          <p style={{ fontSize: 11, color: "#78716C", marginTop: 8 }}>{member.membershipId}</p>
          <p style={{ fontSize: 12, fontWeight: 600, color: "#1C1917" }}>{member.name}</p>
          <p style={{ fontSize: 10, color: "#059669" }}>แตะเพื่อกลับ</p>
        </div>
      </div>
    </div>
  );
};

// ─── Bottom Navigation ────────────────────────────────────────
const BottomNav = ({ active, onChange, role }) => {
  const memberTabs = [
    { id: "dashboard", icon: "⊞", label: "หน้าหลัก" },
    { id: "health", icon: "📊", label: "สุขภาพ" },
    { id: "program", icon: "🏋️", label: "โปรแกรม" },
    { id: "card", icon: "🪪", label: "บัตรสมาชิก" },
  ];
  const adminTabs = [
    { id: "admin", icon: "⊞", label: "ภาพรวม" },
    { id: "members", icon: "👥", label: "สมาชิก" },
    { id: "checkin", icon: "✅", label: "Check-in" },
    { id: "report", icon: "📈", label: "รายงาน" },
  ];
  const tabs = role === "admin" ? adminTabs : memberTabs;
  return (
    <div style={{
      position: "fixed", bottom: 0, left: "50%", transform: "translateX(-50%)",
      width: "100%", maxWidth: 430,
      background: "white", borderTop: "1px solid #E7E5E4",
      display: "flex", zIndex: 100,
      paddingBottom: "env(safe-area-inset-bottom, 8px)",
    }}>
      {tabs.map(tab => (
        <button
          key={tab.id}
          onClick={() => onChange(tab.id)}
          style={{
            flex: 1, padding: "10px 0",
            display: "flex", flexDirection: "column",
            alignItems: "center", gap: 3,
            color: active === tab.id ? C.primary : C.textMuted,
            background: "none", border: "none", cursor: "pointer",
          }}
        >
          <span style={{ fontSize: 20 }}>{tab.icon}</span>
          <span style={{
            fontSize: 10, fontWeight: active === tab.id ? 600 : 400,
            fontFamily: "'Sarabun', sans-serif",
          }}>{tab.label}</span>
        </button>
      ))}
    </div>
  );
};

// ─── Member Dashboard ─────────────────────────────────────────
const MemberDashboard = () => {
  const a = LATEST_ASSESSMENT;
  const bmiStatus = a.bmi < 18.5 ? "น้ำหนักน้อย" : a.bmi < 23 ? "ปกติ" : a.bmi < 25 ? "น้ำหนักเกิน" : "อ้วน";
  const bmiColor = a.bmi < 18.5 ? "#3B82F6" : a.bmi < 23 ? "#059669" : a.bmi < 25 ? "#D97706" : "#DC2626";
  return (
    <div style={{ paddingBottom: 90 }}>
      {/* Header */}
      <div style={{
        background: "linear-gradient(135deg, #047857, #059669)",
        padding: "20px 20px 32px",
        color: "white",
      }}>
        <p style={{ fontSize: 13, opacity: 0.8 }}>สวัสดี 👋</p>
        <h2 style={{ fontSize: 22, fontWeight: 700, margin: "2px 0" }}>คุณ{MEMBER.nickname}</h2>
        <p style={{ fontSize: 13, opacity: 0.8 }}>เหลืออีก {MEMBER.daysRemaining} วัน · {MEMBER.package}</p>
      </div>

      <div style={{ padding: "0 16px", marginTop: -16 }}>
        {/* Membership Status Card */}
        <Card className="p-4 mb-4">
          <div className="flex justify-between items-center mb-3">
            <div>
              <p className="text-xs text-stone-500">สมาชิก #</p>
              <p className="font-semibold text-stone-800">{MEMBER.membershipId}</p>
            </div>
            <Badge color={MEMBER.daysRemaining <= 7 ? "yellow" : "green"}>
              {MEMBER.daysRemaining <= 7 ? "⚠️ ใกล้หมดอายุ" : "● ใช้งานอยู่"}
            </Badge>
          </div>
          <div style={{
            height: 6, background: "#F0FDF4", borderRadius: 99, overflow: "hidden"
          }}>
            <div style={{
              height: "100%", borderRadius: 99,
              width: `${Math.max(5, (MEMBER.daysRemaining / 30) * 100)}%`,
              background: MEMBER.daysRemaining <= 7 ? "#D97706" : "#059669",
              transition: "width 0.8s ease",
            }} />
          </div>
          <p className="text-xs text-stone-400 mt-1.5">
            หมดอายุ {MEMBER.endDate} · โค้ช {MEMBER.coachName}
          </p>
        </Card>

        {/* Attendance Bar Chart */}
        <Card className="p-4 mb-4">
          <p className="text-sm font-semibold text-stone-700 mb-3">การเข้าใช้ (รายสัปดาห์)</p>
          <ResponsiveContainer width="100%" height={100}>
            <BarChart data={WEEKLY_CHECKINS} barSize={28}>
              <XAxis dataKey="week" tick={{ fontSize: 11, fill: "#78716C" }} axisLine={false} tickLine={false} />
              <Bar dataKey="days" fill="#D1FAE5" radius={6}>
                {WEEKLY_CHECKINS.map((_, i) => (
                  <rect key={i} fill={i === WEEKLY_CHECKINS.length - 1 ? "#059669" : "#A7F3D0"} />
                ))}
              </Bar>
            </BarChart>
          </ResponsiveContainer>
          <div className="flex justify-between text-xs text-stone-500 mt-1">
            <span>เฉลี่ย 3.8 วัน/สัปดาห์</span>
            <span className="text-emerald-600 font-medium">↑ ดีขึ้น 15%</span>
          </div>
        </Card>

        {/* Latest Health Metrics */}
        <p className="text-sm font-semibold text-stone-700 mb-2">ข้อมูลสุขภาพล่าสุด</p>
        <p className="text-xs text-stone-400 mb-3">ประเมินเมื่อ {a.date}</p>
        <div className="grid grid-cols-2 gap-3 mb-4">
          <MetricCard label="น้ำหนัก" value={a.weight} unit="กก." trend={-1.5} />
          <MetricCard label="BMI" value={a.bmi} unit="" color={bmiColor} />
          <MetricCard label="ไขมัน %" value={a.bodyFat} unit="%" trend={-1.3} color="#F59E0B" />
          <MetricCard label="กล้ามเนื้อ" value={a.muscle} unit="กก." trend={1.2} color="#3B82F6" />
        </div>

        {/* Weight Progress Chart */}
        <Card className="p-4 mb-4">
          <p className="text-sm font-semibold text-stone-700 mb-1">พัฒนาการน้ำหนัก</p>
          <ResponsiveContainer width="100%" height={130}>
            <AreaChart data={HEALTH_DATA}>
              <defs>
                <linearGradient id="wGrad" x1="0" y1="0" x2="0" y2="1">
                  <stop offset="5%" stopColor="#059669" stopOpacity={0.15} />
                  <stop offset="95%" stopColor="#059669" stopOpacity={0} />
                </linearGradient>
              </defs>
              <CartesianGrid strokeDasharray="3 3" stroke="#F0F0EE" />
              <XAxis dataKey="month" tick={{ fontSize: 11, fill: "#78716C" }} axisLine={false} tickLine={false} />
              <YAxis domain={[70, 85]} tick={{ fontSize: 11, fill: "#78716C" }} axisLine={false} tickLine={false} />
              <Tooltip
                contentStyle={{ borderRadius: 12, border: "none", boxShadow: "0 4px 12px rgba(0,0,0,0.1)" }}
                formatter={(v) => [`${v} กก.`, "น้ำหนัก"]}
              />
              <Area type="monotone" dataKey="weight" stroke="#059669" strokeWidth={2.5} fill="url(#wGrad)" dot={{ fill: "#059669", r: 4 }} />
            </AreaChart>
          </ResponsiveContainer>
        </Card>

        {/* Recent Checkins */}
        <Card className="p-4">
          <p className="text-sm font-semibold text-stone-700 mb-3">ประวัติการเข้าใช้ล่าสุด</p>
          <div className="space-y-2.5">
            {CHECKINS.map((c, i) => (
              <div key={i} className="flex items-center justify-between">
                <div className="flex items-center gap-3">
                  <div style={{
                    width: 36, height: 36, borderRadius: 10,
                    background: i === 0 ? "#D1FAE5" : "#F5F5F4",
                    display: "flex", alignItems: "center", justifyContent: "center",
                    fontSize: 16
                  }}>✓</div>
                  <div>
                    <p className="text-sm font-medium text-stone-700">{c.date}</p>
                    <p className="text-xs text-stone-400">{c.time}</p>
                  </div>
                </div>
                <span className="text-xs text-stone-500">{c.duration}</span>
              </div>
            ))}
          </div>
        </Card>
      </div>
    </div>
  );
};

// ─── Health Screen ────────────────────────────────────────────
const HealthScreen = () => {
  const a = LATEST_ASSESSMENT;
  const metrics = [
    { group: "น้ำหนักและองค์ประกอบร่างกาย", items: [
      { label: "น้ำหนัก", value: a.weight, unit: "กก.", icon: "⚖️" },
      { label: "ส่วนสูง", value: a.height, unit: "ซม.", icon: "📏" },
      { label: "BMI", value: a.bmi, unit: "", icon: "📐", note: a.bmi < 23 ? "ปกติ ✓" : "เกินเกณฑ์" },
      { label: "ไขมันในร่างกาย", value: a.bodyFat, unit: "%", icon: "🔥" },
      { label: "มวลกล้ามเนื้อ", value: a.muscle, unit: "กก.", icon: "💪" },
      { label: "ไขมันในช่องท้อง", value: a.visceralFat, unit: "คะแนน", icon: "🫀" },
    ]},
    { group: "ระบบไหลเวียนโลหิต", items: [
      { label: "ความดันโลหิต", value: a.bp, unit: "mmHg", icon: "🩺" },
      { label: "อัตราการเต้นหัวใจ", value: a.heartRate, unit: "bpm", icon: "❤️" },
    ]},
    { group: "สมรรถภาพทางกาย", items: [
      { label: "แรงกำมือซ้าย", value: a.handgripLeft, unit: "กก.", icon: "🤲" },
      { label: "แรงกำมือขวา", value: a.handgripRight, unit: "กก.", icon: "🤲" },
      { label: "TUG Test", value: a.tug, unit: "วินาที", icon: "🏃", note: a.tug < 12 ? "ปกติ ✓" : "ควรพัฒนา" },
    ]},
  ];

  return (
    <div style={{ paddingBottom: 90, padding: "20px 16px 90px" }}>
      <h2 className="text-xl font-bold text-stone-800 mb-1">ข้อมูลสุขภาพ</h2>
      <p className="text-sm text-stone-500 mb-4">ประเมินเมื่อ {a.date}</p>

      {/* Body Fat + Muscle Area Chart */}
      <Card className="p-4 mb-4">
        <p className="text-sm font-semibold text-stone-700 mb-3">แนวโน้มไขมัน vs กล้ามเนื้อ</p>
        <ResponsiveContainer width="100%" height={150}>
          <LineChart data={HEALTH_DATA}>
            <CartesianGrid strokeDasharray="3 3" stroke="#F0F0EE" />
            <XAxis dataKey="month" tick={{ fontSize: 11, fill: "#78716C" }} axisLine={false} tickLine={false} />
            <YAxis tick={{ fontSize: 11, fill: "#78716C" }} axisLine={false} tickLine={false} />
            <Tooltip contentStyle={{ borderRadius: 12, border: "none" }} />
            <Line type="monotone" dataKey="bodyFat" stroke="#F59E0B" strokeWidth={2} dot={{ r: 3 }} name="ไขมัน %" />
            <Line type="monotone" dataKey="muscle" stroke="#3B82F6" strokeWidth={2} dot={{ r: 3 }} name="กล้ามเนื้อ กก." />
          </LineChart>
        </ResponsiveContainer>
      </Card>

      {metrics.map((group, gi) => (
        <div key={gi} className="mb-4">
          <p className="text-xs font-semibold text-stone-400 uppercase tracking-wider mb-2 px-1">{group.group}</p>
          <Card>
            {group.items.map((item, ii) => (
              <div key={ii} className={`flex items-center justify-between px-4 py-3.5 ${ii < group.items.length - 1 ? "border-b border-stone-50" : ""}`}>
                <div className="flex items-center gap-3">
                  <span className="text-lg">{item.icon}</span>
                  <p className="text-sm text-stone-700">{item.label}</p>
                </div>
                <div className="text-right">
                  <p className="text-base font-bold text-stone-800">{item.value} <span className="text-sm font-normal text-stone-400">{item.unit}</span></p>
                  {item.note && <p className="text-xs text-emerald-600">{item.note}</p>}
                </div>
              </div>
            ))}
          </Card>
        </div>
      ))}
    </div>
  );
};

// ─── Program Screen ───────────────────────────────────────────
const ProgramScreen = () => {
  const [activeDay, setActiveDay] = useState(0);
  const day = PROGRAM.days[activeDay];
  return (
    <div style={{ paddingBottom: 90, padding: "20px 16px 90px" }}>
      <h2 className="text-xl font-bold text-stone-800 mb-1">โปรแกรมออกกำลังกาย</h2>
      <p className="text-sm text-stone-500 mb-4">{PROGRAM.name}</p>

      {/* Day Selector */}
      <div className="flex gap-2 mb-4 overflow-x-auto pb-1">
        {PROGRAM.days.map((d, i) => (
          <button
            key={i}
            onClick={() => setActiveDay(i)}
            style={{
              flexShrink: 0,
              padding: "8px 16px",
              borderRadius: 12,
              border: "none",
              background: activeDay === i ? "#059669" : "#F5F5F4",
              color: activeDay === i ? "white" : "#78716C",
              fontWeight: 600,
              fontSize: 13,
              cursor: "pointer",
              fontFamily: "'Sarabun', sans-serif",
            }}
          >
            วันที่ {d.day}
          </button>
        ))}
      </div>

      <Card className="p-4 mb-4">
        <p className="text-sm font-semibold text-emerald-700 mb-3">{day.label}</p>
        <div className="space-y-2">
          {day.exercises.map((ex, i) => (
            <div key={i} style={{
              display: "flex", alignItems: "center", justifyContent: "space-between",
              padding: "12px 14px",
              background: i % 2 === 0 ? "#F8F7F4" : "white",
              borderRadius: 12,
            }}>
              <div className="flex items-center gap-3">
                <span style={{
                  width: 28, height: 28, borderRadius: 8,
                  background: "#D1FAE5", color: "#059669",
                  display: "flex", alignItems: "center", justifyContent: "center",
                  fontWeight: 700, fontSize: 12,
                }}>{i + 1}</span>
                <p className="text-sm font-medium text-stone-700">{ex.name}</p>
              </div>
              <div className="text-right">
                <p className="text-sm font-bold text-stone-800">{ex.sets} เซต × {ex.reps}</p>
                <p className="text-xs text-stone-400">{ex.weight}</p>
              </div>
            </div>
          ))}
        </div>
      </Card>

      <Card className="p-4">
        <p className="text-sm font-semibold text-stone-700 mb-2">📝 หมายเหตุจากโค้ช</p>
        <p className="text-sm text-stone-600 leading-relaxed">
          โฟกัสการบีบกล้ามเนื้อในทุกท่า พักระหว่างเซต 60-90 วินาที
          หากรู้สึกเจ็บปวดให้หยุดทันทีและแจ้งโค้ช
        </p>
      </Card>
    </div>
  );
};

// ─── Admin Dashboard ──────────────────────────────────────────
const AdminDashboard = () => {
  const s = ADMIN_STATS;
  const revenueProgress = (s.monthlyRevenue / (s.yearlyTarget / 12)) * 100;
  return (
    <div style={{ paddingBottom: 90 }}>
      <div style={{ background: "linear-gradient(135deg, #1C1917, #292524)", padding: "20px 20px 28px", color: "white" }}>
        <p className="text-xs opacity-70 mb-1">แผงควบคุม</p>
        <h2 className="text-xl font-bold">FitStudio Pro</h2>
        <p className="text-sm opacity-70">พฤหัส 17 พ.ค. 2568</p>
      </div>

      <div style={{ padding: "0 16px", marginTop: -8 }}>
        {/* Stats Grid */}
        <div className="grid grid-cols-2 gap-3 mb-4">
          {[
            { label: "สมาชิกทั้งหมด", value: s.totalMembers, sub: `${s.activeMembers} คนใช้งานอยู่`, icon: "👥", color: "#059669" },
            { label: "สมาชิกใหม่เดือนนี้", value: s.newThisMonth, sub: "คน", icon: "🆕", color: "#3B82F6" },
            { label: "ใกล้หมดอายุ", value: s.expiringSoon, sub: "ภายใน 7 วัน", icon: "⏰", color: "#D97706" },
            { label: "รายได้เดือนนี้", value: `${(s.monthlyRevenue / 1000).toFixed(0)}k`, sub: "บาท", icon: "💰", color: "#7C3AED" },
          ].map((stat, i) => (
            <Card key={i} className="p-4">
              <div className="flex justify-between items-start mb-2">
                <p className="text-xs text-stone-500">{stat.label}</p>
                <span className="text-lg">{stat.icon}</span>
              </div>
              <p className="text-2xl font-bold" style={{ color: stat.color }}>{stat.value}</p>
              <p className="text-xs text-stone-400 mt-0.5">{stat.sub}</p>
            </Card>
          ))}
        </div>

        {/* Revenue Chart */}
        <Card className="p-4 mb-4">
          <div className="flex justify-between items-center mb-1">
            <p className="text-sm font-semibold text-stone-700">รายได้รายเดือน</p>
            <Badge color="green">+14% YoY</Badge>
          </div>
          <p className="text-xs text-stone-400 mb-3">เป้าหมาย {(s.yearlyTarget / 12 / 1000).toFixed(0)}k บาท/เดือน · {revenueProgress.toFixed(0)}%</p>
          <div style={{ height: 6, background: "#F0FDF4", borderRadius: 99, marginBottom: 12, overflow: "hidden" }}>
            <div style={{ width: `${revenueProgress}%`, height: "100%", background: "#059669", borderRadius: 99, transition: "width 1s ease" }} />
          </div>
          <ResponsiveContainer width="100%" height={130}>
            <BarChart data={REVENUE_DATA} barSize={32}>
              <XAxis dataKey="month" tick={{ fontSize: 11, fill: "#78716C" }} axisLine={false} tickLine={false} />
              <YAxis hide />
              <Tooltip
                contentStyle={{ borderRadius: 12, border: "none" }}
                formatter={(v) => [`฿${v.toLocaleString()}`, "รายได้"]}
              />
              <Bar dataKey="revenue" fill="#D1FAE5" radius={6}>
                {REVENUE_DATA.map((_, i) => (
                  <rect key={i} fill={i === REVENUE_DATA.length - 1 ? "#059669" : "#A7F3D0"} />
                ))}
              </Bar>
            </BarChart>
          </ResponsiveContainer>
        </Card>

        {/* Members List */}
        <p className="text-sm font-semibold text-stone-700 mb-2">สมาชิกล่าสุด / ใกล้หมดอายุ</p>
        <Card>
          {MEMBERS_LIST.map((m, i) => (
            <div key={i} className={`flex items-center justify-between px-4 py-3.5 ${i < MEMBERS_LIST.length - 1 ? "border-b border-stone-50" : ""}`}>
              <div className="flex items-center gap-3">
                <div style={{
                  width: 38, height: 38, borderRadius: 12,
                  background: "#F5F5F4",
                  display: "flex", alignItems: "center", justifyContent: "center",
                  fontSize: 16, fontWeight: 700, color: "#059669",
                }}>
                  {m.name[0]}
                </div>
                <div>
                  <p className="text-sm font-medium text-stone-700">{m.name}</p>
                  <p className="text-xs text-stone-400">{m.package}</p>
                </div>
              </div>
              <div className="text-right">
                <Badge color={m.status === "expired" ? "red" : m.status === "expiring" ? "yellow" : "green"}>
                  {m.status === "expired" ? "หมดอายุ" : m.status === "expiring" ? `${m.daysLeft} วัน` : "ปกติ"}
                </Badge>
              </div>
            </div>
          ))}
        </Card>
      </div>
    </div>
  );
};

// ─── Check-in Screen ─────────────────────────────────────────
const CheckinScreen = ({ role }) => {
  const [scanning, setScanning] = useState(false);
  const [checkedIn, setCheckedIn] = useState(false);

  const handleScan = () => {
    setScanning(true);
    setTimeout(() => {
      setScanning(false);
      setCheckedIn(true);
      setTimeout(() => setCheckedIn(false), 3000);
    }, 2000);
  };

  return (
    <div style={{ padding: "20px 16px 90px" }}>
      <h2 className="text-xl font-bold text-stone-800 mb-1">
        {role === "admin" ? "สแกน QR Check-in" : "บัตรสมาชิก"}
      </h2>
      <p className="text-sm text-stone-500 mb-4">
        {role === "admin" ? "สแกน QR Code ของสมาชิก" : "แสดง QR Code เพื่อ check-in"}
      </p>

      {role === "admin" ? (
        <Card className="p-6 text-center mb-4">
          <div style={{
            width: 200, height: 200, margin: "0 auto 16px",
            background: scanning ? "#D1FAE5" : "#F8F7F4",
            borderRadius: 20,
            display: "flex", alignItems: "center", justifyContent: "center",
            fontSize: 64,
            border: scanning ? "2px solid #059669" : "2px dashed #D6D3D1",
            transition: "all 0.3s",
          }}>
            {checkedIn ? "✅" : scanning ? "🔍" : "📷"}
          </div>
          {checkedIn && (
            <div style={{ background: "#D1FAE5", borderRadius: 12, padding: "12px 16px", marginBottom: 16 }}>
              <p style={{ color: "#059669", fontWeight: 700 }}>✓ Check-in สำเร็จ!</p>
              <p style={{ color: "#047857", fontSize: 13 }}>สุชาติ วงศ์สกุล · 07:24 น.</p>
            </div>
          )}
          <button
            onClick={handleScan}
            disabled={scanning}
            style={{
              background: "#059669", color: "white",
              border: "none", borderRadius: 14, padding: "14px 32px",
              fontSize: 16, fontWeight: 600, cursor: "pointer",
              opacity: scanning ? 0.7 : 1,
              fontFamily: "'Sarabun', sans-serif",
            }}
          >
            {scanning ? "กำลังสแกน..." : "เปิดกล้องสแกน"}
          </button>
        </Card>
      ) : (
        <QRMemberCard member={MEMBER} />
      )}

      <p className="text-sm font-semibold text-stone-700 mt-4 mb-2">ประวัติการเข้าใช้</p>
      <Card>
        {CHECKINS.map((c, i) => (
          <div key={i} className={`flex items-center justify-between px-4 py-3.5 ${i < CHECKINS.length - 1 ? "border-b border-stone-50" : ""}`}>
            <div className="flex items-center gap-3">
              <div style={{
                width: 36, height: 36, borderRadius: 10,
                background: i === 0 ? "#D1FAE5" : "#F5F5F4",
                display: "flex", alignItems: "center", justifyContent: "center",
                fontSize: 15, color: i === 0 ? "#059669" : "#78716C"
              }}>✓</div>
              <div>
                <p className="text-sm font-medium text-stone-700">{c.date}</p>
                <p className="text-xs text-stone-400">{c.time}</p>
              </div>
            </div>
            <span className="text-xs text-stone-400">{c.duration}</span>
          </div>
        ))}
      </Card>
    </div>
  );
};

// ─── TopBar ───────────────────────────────────────────────────
const TopBar = ({ role, onRoleSwitch }) => (
  <div style={{
    background: "white", borderBottom: "1px solid #E7E5E4",
    padding: "14px 20px",
    display: "flex", justifyContent: "space-between", alignItems: "center",
    position: "sticky", top: 0, zIndex: 50,
  }}>
    <div className="flex items-center gap-2">
      <span style={{
        width: 32, height: 32, background: "#059669",
        borderRadius: 8, display: "inline-flex",
        alignItems: "center", justifyContent: "center", fontSize: 16
      }}>💪</span>
      <span style={{ fontWeight: 700, fontSize: 16, color: "#1C1917", fontFamily: "'Sarabun', sans-serif" }}>
        FitStudio Pro
      </span>
    </div>
    <div className="flex items-center gap-2">
      <button
        onClick={onRoleSwitch}
        style={{
          background: "#F5F5F4", border: "none", borderRadius: 8,
          padding: "6px 12px", fontSize: 12, color: "#78716C", cursor: "pointer",
          fontFamily: "'Sarabun', sans-serif",
        }}
      >
        {role === "member" ? "👑 Admin View" : "👤 Member View"}
      </button>
      <div style={{
        width: 34, height: 34, borderRadius: 10,
        background: "#D1FAE5",
        display: "flex", alignItems: "center", justifyContent: "center",
        fontSize: 16, fontWeight: 700, color: "#059669",
      }}>ช</div>
    </div>
  </div>
);

// ─── App Root ─────────────────────────────────────────────────
export default function App() {
  const [role, setRole] = useState("member");
  const [tab, setTab] = useState("dashboard");

  useEffect(() => {
    setTab(role === "admin" ? "admin" : "dashboard");
  }, [role]);

  const renderContent = () => {
    if (role === "member") {
      if (tab === "dashboard") return <MemberDashboard />;
      if (tab === "health") return <HealthScreen />;
      if (tab === "program") return <ProgramScreen />;
      if (tab === "card") return <CheckinScreen role="member" />;
    } else {
      if (tab === "admin") return <AdminDashboard />;
      if (tab === "members") return (
        <div style={{ padding: "20px 16px 90px" }}>
          <h2 className="text-xl font-bold text-stone-800 mb-4">รายชื่อสมาชิก</h2>
          <Card>
            {MEMBERS_LIST.map((m, i) => (
              <div key={i} className={`flex items-center justify-between px-4 py-4 ${i < MEMBERS_LIST.length - 1 ? "border-b border-stone-50" : ""}`}>
                <div className="flex items-center gap-3">
                  <div style={{
                    width: 42, height: 42, borderRadius: 12, background: "#F5F5F4",
                    display: "flex", alignItems: "center", justifyContent: "center",
                    fontSize: 18, fontWeight: 700, color: "#059669",
                  }}>{m.name[0]}</div>
                  <div>
                    <p className="text-sm font-semibold text-stone-800">{m.name}</p>
                    <p className="text-xs text-stone-400">{m.phone} · {m.package}</p>
                  </div>
                </div>
                <Badge color={m.status === "expired" ? "red" : m.status === "expiring" ? "yellow" : "green"}>
                  {m.status === "expired" ? "หมดอายุ" : m.status === "expiring" ? `${m.daysLeft} วัน` : "ปกติ"}
                </Badge>
              </div>
            ))}
          </Card>
        </div>
      );
      if (tab === "checkin") return <CheckinScreen role="admin" />;
      if (tab === "report") return (
        <div style={{ padding: "20px 16px 90px" }}>
          <h2 className="text-xl font-bold text-stone-800 mb-4">รายงาน</h2>
          <Card className="p-4 mb-4">
            <p className="text-sm font-semibold text-stone-700 mb-3">รายได้รายเดือน</p>
            <ResponsiveContainer width="100%" height={200}>
              <AreaChart data={REVENUE_DATA}>
                <defs>
                  <linearGradient id="rGrad" x1="0" y1="0" x2="0" y2="1">
                    <stop offset="5%" stopColor="#059669" stopOpacity={0.2} />
                    <stop offset="95%" stopColor="#059669" stopOpacity={0} />
                  </linearGradient>
                </defs>
                <CartesianGrid strokeDasharray="3 3" stroke="#F0F0EE" />
                <XAxis dataKey="month" tick={{ fontSize: 11, fill: "#78716C" }} axisLine={false} tickLine={false} />
                <YAxis tick={{ fontSize: 11, fill: "#78716C" }} axisLine={false} tickLine={false} tickFormatter={(v) => `${v / 1000}k`} />
                <Tooltip formatter={(v) => [`฿${v.toLocaleString()}`, "รายได้"]} contentStyle={{ borderRadius: 12, border: "none" }} />
                <Area type="monotone" dataKey="revenue" stroke="#059669" strokeWidth={2.5} fill="url(#rGrad)" dot={{ fill: "#059669", r: 4 }} />
              </AreaChart>
            </ResponsiveContainer>
          </Card>
          <div className="grid grid-cols-2 gap-3">
            {[
              { label: "รายได้เดือนนี้", value: "฿186,430", color: "#059669" },
              { label: "สมาชิกใหม่", value: "23 คน", color: "#3B82F6" },
              { label: "สมาชิกหมดอายุ", value: "5 คน", color: "#DC2626" },
              { label: "อัตราต่ออายุ", value: "78%", color: "#7C3AED" },
            ].map((s, i) => (
              <Card key={i} className="p-4">
                <p className="text-xs text-stone-500 mb-1">{s.label}</p>
                <p className="text-xl font-bold" style={{ color: s.color }}>{s.value}</p>
              </Card>
            ))}
          </div>
        </div>
      );
    }
  };

  return (
    <div style={{
      fontFamily: "'Sarabun', 'IBM Plex Sans Thai', sans-serif",
      background: C.bg,
      minHeight: "100vh",
      maxWidth: 430,
      margin: "0 auto",
      position: "relative",
      overflowX: "hidden",
    }}>
      <link href="https://fonts.googleapis.com/css2?family=Sarabun:wght@400;500;600;700&display=swap" rel="stylesheet" />
      <TopBar role={role} onRoleSwitch={() => setRole(r => r === "member" ? "admin" : "member")} />
      <div style={{ overflowY: "auto", height: "calc(100vh - 57px)" }}>
        {renderContent()}
      </div>
      <BottomNav active={tab} onChange={setTab} role={role} />
    </div>
  );
}
