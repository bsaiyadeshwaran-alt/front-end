 { useState, useEffect, useRef } from "react";

// ════════════════════════════════════════════════════════════════════
// DUMMY DATA
// ════════════════════════════════════════════════════════════════════
const USERS = {import
  admin:   { email:"admin@edu.in",   password:"admin123",   name:"Dr. R. Sharma",    role:"admin",   avatar:"RS" },
  faculty: { email:"faculty@edu.in", password:"faculty123", name:"Prof. A. Mehta",   role:"faculty", avatar:"AM", dept:"Computer Science", empId:"FAC2024", subjects:["DBMS","OS","Networks"] },
  student: { email:"student@edu.in", password:"student123", name:"Arjun Krishnan",   role:"student", avatar:"AK", rollNo:"CS21B042", dept:"Computer Science", sem:"6th", attendance:68, cgpa:8.4, leavesLeft:3, skillrack:42, leetcode:18 },
};

const STUDENTS = [
  {id:1,name:"Arjun Krishnan",  roll:"CS21B042",dept:"CS",sem:6,att:68, cgpa:8.4,risk:"High",   activity:40,badges:["Assignment Streak"]},
  {id:2,name:"Priya Nair",     roll:"CS21B043",dept:"CS",sem:6,att:82, cgpa:9.1,risk:"Low",    activity:88,badges:["Perfect Attendance","Top 5 in Class"]},
  {id:3,name:"Rahul Varma",    roll:"CS21B044",dept:"CS",sem:6,att:75, cgpa:7.8,risk:"Medium", activity:65,badges:[]},
  {id:4,name:"Sneha Pillai",   roll:"CS21B045",dept:"CS",sem:6,att:91, cgpa:9.6,risk:"Low",    activity:95,badges:["Perfect Attendance","Top 5 in Class","Assignment Streak"]},
  {id:5,name:"Karthik Bose",   roll:"CS21B046",dept:"CS",sem:6,att:55, cgpa:6.9,risk:"High",   activity:30,badges:[]},
  {id:6,name:"Meera Joshi",    roll:"ME21B012",dept:"ME",sem:4,att:88, cgpa:8.2,risk:"Low",    activity:72,badges:["Assignment Streak"]},
  {id:7,name:"Dev Anand",      roll:"CS21B047",dept:"CS",sem:6,att:62, cgpa:7.2,risk:"High",   activity:35,badges:[]},
  {id:8,name:"Kavitha Rajan",  roll:"ECE21B08",dept:"ECE",sem:5,att:79, cgpa:8.0,risk:"Low",   activity:70,badges:["Top 5 in Class"]},
];

const FACULTY_LIST = [
  {id:1,name:"Prof. A. Mehta", empId:"FAC2024",dept:"CS", subjects:"DBMS, OS",      att:96, passRate:82, rating:4.5},
  {id:2,name:"Dr. S. Iyer",    empId:"FAC2019",dept:"CS", subjects:"DS, Algorithms", att:92, passRate:78, rating:4.2},
  {id:3,name:"Prof. R. Kumar", empId:"FAC2021",dept:"ME", subjects:"Thermodynamics", att:88, passRate:71, rating:3.9},
  {id:4,name:"Dr. P. Singh",   empId:"FAC2018",dept:"ECE",subjects:"Digital Electronics",att:95,passRate:85,rating:4.7},
  {id:5,name:"Dr. M. Rao",     empId:"FAC2022",dept:"CS", subjects:"Maths III",     att:90, passRate:74, rating:4.1},
];

const TIMETABLE = [
  {day:"Monday",    slots:["DBMS (9-10)","OS (10-11)","BREAK","Algo (11-12)","Lab (2-4)","—"]},
  {day:"Tuesday",   slots:["Networks (9-10)","DBMS (10-11)","BREAK","OS Lab (11-1)","—","—"]},
  {day:"Wednesday", slots:["Algo (9-10)","Maths (10-11)","BREAK","DBMS (11-12)","Library (2-3)","—"]},
  {day:"Thursday",  slots:["OS (9-10)","Networks (10-11)","BREAK","Project (11-1)","—","—"]},
  {day:"Friday",    slots:["Maths (9-10)","Seminar (10-11)","BREAK","Algo (11-12)","Sports (2-4)","—"]},
];

const FACULTY_AVAIL = [
  {name:"Prof. A. Mehta", dept:"CS", slots:{Mon:["In Class","Available","—","In Class","Available"],Tue:["Available","In Class","—","Available","—"],Wed:["In Class","Available","—","Available","—"],Thu:["Available","In Class","—","In Class","—"],Fri:["In Class","Available","—","Available","—"]}},
  {name:"Dr. S. Iyer",   dept:"CS", slots:{Mon:["Available","In Class","—","Available","In Class"],Tue:["In Class","Available","—","In Class","—"],Wed:["Available","In Class","—","In Class","—"],Thu:["In Class","Available","—","Available","—"],Fri:["Available","In Class","—","Available","—"]}},
  {name:"Dr. M. Rao",    dept:"CS", slots:{Mon:["In Class","Available","—","On Leave","Available"],Tue:["Available","Available","—","In Class","—"],Wed:["In Class","In Class","—","Available","—"],Thu:["Available","Available","—","In Class","—"],Fri:["In Class","Available","—","Available","—"]}},
];

const INTERNAL_MARKS = [
  {subject:"DBMS",              ia1:22,ia2:24,ia3:21,max:25},
  {subject:"Operating Systems", ia1:18,ia2:20,ia3:23,max:25},
  {subject:"Computer Networks", ia1:21,ia2:19,ia3:22,max:25},
  {subject:"Algorithms",        ia1:24,ia2:23,ia3:22,max:25},
  {subject:"Mathematics III",   ia1:20,ia2:18,ia3:21,max:25},
];

const SEM_MARKS = [
  {subject:"Data Structures",      int:45,ext:68,total:113,max:150,grade:"A"},
  {subject:"Computer Architecture",int:42,ext:71,total:113,max:150,grade:"A"},
  {subject:"Discrete Mathematics", int:38,ext:62,total:100, max:150,grade:"B+"},
  {subject:"Digital Electronics",  int:47,ext:74,total:121,max:150,grade:"A+"},
  {subject:"Python Programming",   int:44,ext:69,total:113,max:150,grade:"A"},
];

const ATT_DETAIL = [
  {subject:"DBMS",             total:45,present:29,pct:64},
  {subject:"Operating Systems",total:40,present:32,pct:80},
  {subject:"Networks",         total:38,present:26,pct:68},
  {subject:"Algorithms",       total:42,present:36,pct:86},
  {subject:"Mathematics III",  total:35,present:24,pct:69},
];

const ACTIVITIES = [
  {id:1,name:"Annual Tech Fest – TechSurge 2025",date:"2025-03-22",status:"Upcoming",type:"Technical",part:320},
  {id:2,name:"Sports Day – Inter-Department",     date:"2025-03-15",status:"Upcoming",type:"Sports",   part:280},
  {id:3,name:"Guest Lecture: AI & Careers",       date:"2025-03-10",status:"Pending", type:"Academic", part:0},
  {id:4,name:"Alumni Interaction Meet",           date:"2025-03-28",status:"Upcoming",type:"Cultural", part:0},
  {id:5,name:"Workshop: Cloud Computing",         date:"2025-02-28",status:"Done",    type:"Technical",part:95},
];

const WEEKLY_CODING = [
  {week:"W1",skillrack:8, leetcode:3},
  {week:"W2",skillrack:12,leetcode:5},
  {week:"W3",skillrack:7, leetcode:4},
  {week:"W4",skillrack:15,leetcode:6},
  {week:"W5",skillrack:10,leetcode:4},
  {week:"W6",skillrack:13,leetcode:7},
  {week:"W7",skillrack:9, leetcode:5},
  {week:"W8",skillrack:14,leetcode:6},
];

const ASSIGNMENTS = [
  {id:1,title:"ER Diagram Design",     subject:"DBMS",  due:"2025-03-15",submitted:38,total:45,status:"Active"},
  {id:2,title:"Process Scheduling",    subject:"OS",    due:"2025-03-20",submitted:42,total:45,status:"Active"},
  {id:3,title:"Shortest Path Impl.",   subject:"Algo",  due:"2025-03-10",submitted:44,total:45,status:"Closed"},
];

const DEPT_HEALTH = [
  {dept:"Computer Science",  att:76,marks:78,activity:72,score:75},
  {dept:"Mechanical Engg",   att:82,marks:74,activity:68,score:75},
  {dept:"Electronics",       att:79,marks:81,activity:75,score:78},
  {dept:"Civil Engg",        att:71,marks:69,activity:60,score:67},
  {dept:"Electrical",        att:85,marks:77,activity:80,score:81},
];

const ANNOUNCEMENTS_HIST = [
  {title:"Semester End Exam Schedule Released",  target:"All",     date:"2025-03-01"},
  {title:"Holiday – Holi Celebration",           target:"All",     date:"2025-02-28"},
  {title:"Faculty Meeting – 10th March",         target:"Faculty", date:"2025-02-25"},
  {title:"Sports Day Registration Open",         target:"Students",date:"2025-02-20"},
];

const LEAVES = [
  {id:1,student:"Arjun Krishnan",roll:"CS21B042",from:"2025-03-05",to:"2025-03-06",reason:"Medical",  status:"Approved"},
  {id:2,student:"Priya Nair",    roll:"CS21B043",from:"2025-03-10",to:"2025-03-10",reason:"Hackathon OD",status:"Pending"},
  {id:3,student:"Karthik Bose",  roll:"CS21B046",from:"2025-03-12",to:"2025-03-14",reason:"Family",   status:"Pending"},
];

const LIBRARY_NOTES = [
  {id:1,subject:"DBMS",     unit:"Unit 2",title:"ER Diagram & Normalization",     faculty:"Prof. A. Mehta",date:"2025-02-20",type:"PDF"},
  {id:2,subject:"OS",       unit:"Unit 3",title:"Process Scheduling Algorithms",  faculty:"Prof. A. Mehta",date:"2025-02-18",type:"PDF"},
  {id:3,subject:"Networks", unit:"Unit 1",title:"OSI Model & TCP/IP",             faculty:"Dr. S. Iyer",  date:"2025-02-15",type:"PPTX"},
  {id:4,subject:"Algorithms",unit:"Unit 4",title:"Graph Algorithms – BFS & DFS",  faculty:"Dr. S. Iyer",  date:"2025-02-12",type:"PDF"},
  {id:5,subject:"Maths III",unit:"Unit 2",title:"Fourier Transforms",             faculty:"Dr. M. Rao",   date:"2025-02-10",type:"DOCX"},
];

const NOTIFICATIONS = [
  {id:1,msg:"Your attendance in DBMS dropped below 70%",     type:"danger", time:"2h ago"},
  {id:2,msg:"IA3 marks uploaded for Operating Systems",      type:"info",   time:"4h ago"},
  {id:3,msg:"Assignment due: ER Diagram – 2 days left",      type:"warning",time:"1d ago"},
  {id:4,msg:"Tech Fest registration closes tomorrow",        type:"info",   time:"1d ago"},
  {id:5,msg:"Faculty feedback form is now open",             type:"success",time:"2d ago"},
];

// ════════════════════════════════════════════════════════════════════
// GLOBAL CSS
// ════════════════════════════════════════════════════════════════════
const CSS = `
@import url('https://fonts.googleapis.com/css2?family=Syne:wght@600;700;800&family=Outfit:wght@300;400;500;600;700&display=swap');

*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --ink:#0d1b2a;--ink2:#1b2e45;--ink3:#243d5f;
  --teal:#0ea5c9;--teal2:#38bdf8;--teal3:#bae6fd;
  --amber:#f59e0b;--amber2:#fbbf24;
  --rose:#f43f5e;--green:#10b981;--purple:#8b5cf6;
  --cream:#f0f7ff;--white:#ffffff;
  --border:#dbeafe;--border2:#e0f2fe;
  --slate:#64748b;--slate2:#94a3b8;--slate3:#cbd5e1;
  --shadow:0 4px 24px rgba(14,165,201,.13);
  --shadow-sm:0 2px 8px rgba(14,165,201,.08);
  --shadow-lg:0 12px 40px rgba(14,165,201,.18);
  --r:14px;--r-sm:8px;--r-lg:20px;
  --sw:268px;--sc:72px;
  --font:'Outfit',sans-serif;
  --font-d:'Syne',sans-serif;
}
body{font-family:var(--font);background:var(--cream);color:var(--ink);overflow-x:hidden}
::-webkit-scrollbar{width:5px;height:5px}
::-webkit-scrollbar-track{background:var(--cream)}
::-webkit-scrollbar-thumb{background:var(--teal3);border-radius:4px}

.lp{min-height:100vh;display:flex;align-items:center;justify-content:center;padding:24px;
  background:radial-gradient(ellipse at 20% 50%,#0d3b5e 0%,#0d1b2a 60%);position:relative;overflow:hidden}
.lp::before{content:'';position:absolute;inset:0;
  background:url("data:image/svg+xml,%3Csvg width='80' height='80' viewBox='0 0 80 80' xmlns='http://www.w3.org/2000/svg'%3E%3Cg fill='%230ea5c9' fill-opacity='0.04'%3E%3Ccircle cx='40' cy='40' r='20'/%3E%3C/g%3E%3C/svg%3E");
  animation:bgmove 20s linear infinite}
@keyframes bgmove{from{background-position:0 0}to{background-position:80px 80px}}
.lp-orb{position:absolute;border-radius:50%;filter:blur(80px);pointer-events:none}
.lp-orb1{width:400px;height:400px;background:rgba(14,165,201,.18);top:-100px;right:-100px}
.lp-orb2{width:300px;height:300px;background:rgba(245,158,11,.12);bottom:-80px;left:-80px}

.lp-wrap{width:100%;max-width:520px;position:relative;z-index:1}
.lp-brand{text-align:center;margin-bottom:36px}
.lp-logo{width:68px;height:68px;background:linear-gradient(135deg,var(--teal),var(--teal2));
  border-radius:20px;margin:0 auto 16px;display:flex;align-items:center;justify-content:center;
  font-size:30px;box-shadow:0 8px 32px rgba(14,165,201,.4);animation:logopop .6s ease}
@keyframes logopop{from{transform:scale(.7) rotate(-10deg);opacity:0}to{transform:scale(1) rotate(0);opacity:1}}
.lp-brand h1{font-family:var(--font-d);font-size:2.2rem;color:#fff;letter-spacing:-1px}
.lp-brand p{color:rgba(255,255,255,.45);font-size:.88rem;margin-top:4px}

.role-tabs{display:grid;grid-template-columns:repeat(3,1fr);gap:10px;margin-bottom:28px}
.role-tab{padding:18px 12px;border-radius:var(--r);cursor:pointer;border:2px solid transparent;
  background:rgba(255,255,255,.05);transition:all .25s;text-align:center;color:#fff}
.role-tab:hover{background:rgba(255,255,255,.1);transform:translateY(-2px)}
.role-tab.active{border-color:var(--teal);background:rgba(14,165,201,.15);box-shadow:0 0 24px rgba(14,165,201,.25)}
.role-tab .ri{font-size:1.8rem;margin-bottom:6px}
.role-tab .rn{font-size:.78rem;font-weight:700;letter-spacing:.8px;text-transform:uppercase}

.lp-form{background:rgba(255,255,255,.07);backdrop-filter:blur(20px);
  border:1px solid rgba(255,255,255,.12);border-radius:var(--r-lg);padding:32px}
.lp-form h2{font-family:var(--font-d);font-size:1.35rem;color:#fff;margin-bottom:22px}
.fg{margin-bottom:16px}
.fg label{display:block;color:rgba(255,255,255,.55);font-size:.75rem;font-weight:600;
  text-transform:uppercase;letter-spacing:.6px;margin-bottom:6px}
.fg input{width:100%;padding:12px 14px;border-radius:var(--r-sm);border:1.5px solid rgba(255,255,255,.15);
  background:rgba(255,255,255,.08);color:#fff;font-family:var(--font);font-size:.95rem;transition:border .2s}
.fg input::placeholder{color:rgba(255,255,255,.28)}
.fg input:focus{outline:none;border-color:var(--teal);background:rgba(14,165,201,.1)}
.lp-btn{width:100%;padding:13px;border-radius:var(--r-sm);border:none;
  background:linear-gradient(135deg,var(--teal),var(--teal2));color:#fff;font-weight:700;
  font-size:1rem;cursor:pointer;font-family:var(--font);transition:all .2s;margin-top:6px;
  box-shadow:0 4px 20px rgba(14,165,201,.4)}
.lp-btn:hover{transform:translateY(-1px);box-shadow:0 8px 28px rgba(14,165,201,.5)}
.lp-err{color:#fca5a5;font-size:.82rem;margin-top:10px;text-align:center}
.lp-hint{color:rgba(255,255,255,.3);font-size:.73rem;margin-top:14px;text-align:center;line-height:1.7}

.dash{display:flex;min-height:100vh}

.sidebar{width:var(--sw);min-height:100vh;background:var(--ink);display:flex;
  flex-direction:column;transition:width .3s ease;flex-shrink:0;position:relative;z-index:100;
  box-shadow:4px 0 24px rgba(0,0,0,.25)}
.sidebar.col{width:var(--sc)}
.sb-head{padding:18px 14px;border-bottom:1px solid rgba(255,255,255,.06);
  display:flex;align-items:center;gap:10px}
.sb-logo{width:36px;height:36px;background:linear-gradient(135deg,var(--teal),var(--teal2));
  border-radius:10px;display:flex;align-items:center;justify-content:center;font-size:16px;flex-shrink:0}
.sb-title{font-family:var(--font-d);color:#fff;font-size:1rem;white-space:nowrap;overflow:hidden}
.sb-title span{display:block;color:rgba(255,255,255,.35);font-family:var(--font);
  font-size:.65rem;font-weight:400;text-transform:uppercase;letter-spacing:1px;margin-top:1px}
.sb-user{padding:14px;border-bottom:1px solid rgba(255,255,255,.06);
  display:flex;align-items:center;gap:10px}
.sb-av{width:34px;height:34px;border-radius:9px;background:linear-gradient(135deg,var(--teal),var(--amber));
  display:flex;align-items:center;justify-content:center;font-weight:800;color:#fff;
  font-size:.82rem;flex-shrink:0;font-family:var(--font-d)}
.sb-ui{overflow:hidden}
.sb-uname{color:#fff;font-size:.84rem;font-weight:600;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.sb-urole{color:rgba(255,255,255,.35);font-size:.68rem;text-transform:uppercase;letter-spacing:.5px}
.sb-nav{flex:1;padding:10px 8px;overflow-y:auto}
.sb-section{color:rgba(255,255,255,.22);font-size:.62rem;text-transform:uppercase;
  letter-spacing:1.8px;padding:10px 10px 5px;font-weight:700}
.ni{display:flex;align-items:center;gap:11px;padding:9px 11px;border-radius:var(--r-sm);
  cursor:pointer;color:rgba(255,255,255,.55);font-size:.86rem;transition:all .2s;
  margin-bottom:2px;white-space:nowrap;overflow:hidden;position:relative}
.ni:hover{background:rgba(255,255,255,.07);color:#fff}
.ni.act{background:rgba(14,165,201,.18);color:var(--teal2);font-weight:600}
.ni.act::before{content:'';position:absolute;left:0;top:50%;transform:translateY(-50%);
  width:3px;height:60%;background:var(--teal);border-radius:0 3px 3px 0}
.ni .ic{font-size:1rem;flex-shrink:0;width:20px;text-align:center}
.ni .lb{overflow:hidden;text-overflow:ellipsis}
.sb-bot{padding:10px 8px;border-top:1px solid rgba(255,255,255,.06)}
.col-btn{width:100%;display:flex;align-items:center;gap:11px;padding:9px 11px;
  border-radius:var(--r-sm);border:none;background:transparent;color:rgba(255,255,255,.35);
  cursor:pointer;font-family:var(--font);font-size:.84rem;transition:all .2s;overflow:hidden}
.col-btn:hover{background:rgba(255,255,255,.06);color:#fff}

.main{flex:1;display:flex;flex-direction:column;overflow:hidden}
.topbar{height:62px;background:var(--white);border-bottom:1px solid var(--border);
  display:flex;align-items:center;padding:0 24px;justify-content:space-between;
  flex-shrink:0;box-shadow:var(--shadow-sm)}
.tb-l h2{font-family:var(--font-d);font-size:1.15rem;color:var(--ink)}
.tb-l p{color:var(--slate2);font-size:.75rem;margin-top:1px}
.tb-r{display:flex;align-items:center;gap:12px}
.tb-notif{position:relative;cursor:pointer;width:36px;height:36px;border-radius:var(--r-sm);
  background:var(--cream);border:1px solid var(--border);display:flex;align-items:center;justify-content:center;font-size:1rem}
.tb-notif-dot{position:absolute;top:6px;right:6px;width:8px;height:8px;background:var(--rose);border-radius:50%;border:2px solid #fff}
.tb-badge{background:var(--ink);color:#fff;border-radius:var(--r-sm);padding:5px 12px;font-size:.75rem;font-weight:700;letter-spacing:.5px}
.tb-dark{width:34px;height:34px;border-radius:var(--r-sm);border:1px solid var(--border);
  background:var(--cream);display:flex;align-items:center;justify-content:center;cursor:pointer;font-size:1rem;transition:all .2s}
.tb-dark:hover{border-color:var(--teal);background:var(--teal3)}
.lo-btn{padding:7px 14px;border-radius:var(--r-sm);border:1.5px solid var(--border);
  background:transparent;color:var(--slate);cursor:pointer;font-size:.82rem;
  font-family:var(--font);transition:all .2s;font-weight:500}
.lo-btn:hover{border-color:var(--rose);color:var(--rose)}
.page{flex:1;overflow-y:auto;padding:26px;background:var(--cream)}

body.dark-mode{background:#0b1829!important}
body.dark-mode .topbar{background:#0f2035!important;border-color:#1e3a5f!important}
body.dark-mode .page{background:#0b1829!important}
body.dark-mode .form-card,body.dark-mode .tc,body.dark-mode .sc{background:#0f2035!important;border-color:#1e3a5f!important;color:#e2e8f0!important}
body.dark-mode .tb-l h2{color:#e2e8f0}
body.dark-mode .tb-l p{color:#7fb3d3}
body.dark-mode td,body.dark-mode th{color:#cfe2f0!important}
body.dark-mode th{background:#0d1b2a!important}
body.dark-mode tr:hover td{background:#132d4a!important}
body.dark-mode .form-group2 input,body.dark-mode .form-group2 select,body.dark-mode .form-group2 textarea{background:#132d4a!important;border-color:#1e3a5f!important;color:#e2e8f0!important}
body.dark-mode .section-title{color:#e2e8f0!important}
body.dark-mode .stat-card{background:#0f2035!important;border-color:#1e3a5f!important}
body.dark-mode .sc-val{color:#e2e8f0!important}
body.dark-mode .sc-label{color:#7fb3d3!important}
body.dark-mode .lo-btn{border-color:#1e3a5f;color:#7fb3d3}
body.dark-mode .tb-badge{background:#1e3a5f}
body.dark-mode .tb-dark,body.dark-mode .tb-notif{background:#132d4a;border-color:#1e3a5f}

.stats-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(190px,1fr));gap:18px;margin-bottom:26px}
.stat-card{background:var(--white);border-radius:var(--r);padding:22px;box-shadow:var(--shadow-sm);
  border:1px solid var(--border);position:relative;overflow:hidden;transition:all .25s;cursor:default}
.stat-card:hover{box-shadow:var(--shadow);transform:translateY(-2px)}
.sc-icon{width:42px;height:42px;border-radius:11px;display:flex;align-items:center;
  justify-content:center;font-size:1.2rem;margin-bottom:13px}
.sc-val{font-size:1.85rem;font-weight:800;color:var(--ink);font-family:var(--font-d);line-height:1}
.sc-label{color:var(--slate);font-size:.8rem;font-weight:500;margin-top:4px}
.sc-sub{color:var(--slate2);font-size:.73rem;margin-top:5px}
.sc-trend{position:absolute;top:18px;right:18px;font-size:.72rem;font-weight:700;padding:3px 8px;border-radius:20px}

.tc{background:var(--white);border-radius:var(--r);border:1px solid var(--border);
  box-shadow:var(--shadow-sm);overflow:hidden;margin-bottom:20px}
.tc table{width:100%;border-collapse:collapse}
.tc th{background:#f8fbff;padding:11px 15px;text-align:left;font-size:.72rem;
  font-weight:700;color:var(--slate);text-transform:uppercase;letter-spacing:.5px;border-bottom:1px solid var(--border)}
.tc td{padding:12px 15px;border-bottom:1px solid var(--border2);font-size:.86rem;color:#334155}
.tc tr:last-child td{border-bottom:none}
.tc tr:hover td{background:#f8fbff}

.b{display:inline-flex;align-items:center;padding:3px 9px;border-radius:20px;font-size:.71rem;font-weight:700}
.b-g{background:#d1fae5;color:#065f46}.b-r{background:#fee2e2;color:#991b1b}
.b-a{background:#fef3c7;color:#92400e}.b-b{background:#dbeafe;color:#1e40af}
.b-p{background:#ede9fe;color:#5b21b6}.b-s{background:#f1f5f9;color:#475569}
.b-t{background:#ccfbf1;color:#0f766e}

.alert{padding:13px 16px;border-radius:var(--r-sm);margin-bottom:18px;
  display:flex;align-items:flex-start;gap:10px;font-size:.87rem;font-weight:500;line-height:1.5}
.al-w{background:#fef3c7;border:1px solid #fbbf24;color:#92400e}
.al-d{background:#fee2e2;border:1px solid #fca5a5;color:#991b1b}
.al-s{background:#d1fae5;border:1px solid #6ee7b7;color:#065f46}
.al-i{background:#dbeafe;border:1px solid #93c5fd;color:#1e40af}

.form-card{background:var(--white);border-radius:var(--r);padding:26px;
  border:1px solid var(--border);box-shadow:var(--shadow-sm);margin-bottom:20px}
.form-card h3{font-family:var(--font-d);font-size:1.05rem;color:var(--ink);
  margin-bottom:20px;padding-bottom:12px;border-bottom:1px solid var(--border)}
.fr{display:grid;grid-template-columns:1fr 1fr;gap:16px;margin-bottom:16px}
.fr1{grid-template-columns:1fr}
.form-group2{display:flex;flex-direction:column;gap:5px}
.form-group2 label{font-size:.75rem;font-weight:700;color:var(--slate);text-transform:uppercase;letter-spacing:.4px}
.form-group2 input,.form-group2 select,.form-group2 textarea{padding:10px 12px;
  border-radius:var(--r-sm);border:1.5px solid var(--border);font-family:var(--font);
  font-size:.9rem;color:#334155;transition:border .2s;background:#fff}
.form-group2 input:focus,.form-group2 select:focus,.form-group2 textarea:focus{outline:none;border-color:var(--teal)}
.form-group2 textarea{resize:vertical;min-height:88px}
.btn-p{padding:10px 22px;border-radius:var(--r-sm);border:none;background:linear-gradient(135deg,var(--ink),var(--ink2));
  color:#fff;font-weight:700;font-size:.88rem;cursor:pointer;font-family:var(--font);
  transition:all .2s;box-shadow:0 4px 14px rgba(13,27,42,.25)}
.btn-p:hover{transform:translateY(-1px);box-shadow:0 6px 20px rgba(13,27,42,.3)}
.btn-s{padding:9px 20px;border-radius:var(--r-sm);border:1.5px solid var(--border);
  background:transparent;color:var(--slate);font-weight:500;font-size:.88rem;
  cursor:pointer;font-family:var(--font);transition:all .2s;margin-left:8px}
.btn-s:hover{border-color:var(--ink);color:var(--ink)}
.btn-t{padding:7px 14px;border-radius:var(--r-sm);border:1.5px solid var(--teal3);
  background:rgba(14,165,201,.07);color:var(--teal);font-size:.79rem;font-weight:700;
  cursor:pointer;font-family:var(--font);transition:all .2s}
.btn-t:hover{background:rgba(14,165,201,.15)}
.btn-d{padding:6px 13px;border-radius:var(--r-sm);border:1.5px solid #fca5a5;
  background:transparent;color:var(--rose);font-size:.79rem;font-weight:700;cursor:pointer;font-family:var(--font);transition:all .2s}
.btn-d:hover{background:#fee2e2}

.sh{display:flex;align-items:center;justify-content:space-between;margin-bottom:14px}
.section-title{font-family:var(--font-d);font-size:1.05rem;color:var(--ink)}
.section-sub{color:var(--slate2);font-size:.78rem;margin-top:2px}

.wc{background:linear-gradient(135deg,var(--ink) 0%,var(--ink3) 100%);border-radius:var(--r);
  padding:26px 30px;margin-bottom:22px;display:flex;align-items:center;
  justify-content:space-between;box-shadow:0 8px 32px rgba(13,27,42,.25);position:relative;overflow:hidden}
.wc::after{content:'🎓';position:absolute;right:-10px;bottom:-20px;font-size:8rem;opacity:.06}
.wc h2{font-family:var(--font-d);font-size:1.45rem;color:#fff}
.wc p{color:rgba(255,255,255,.55);font-size:.86rem;margin-top:5px}
.wc-r{text-align:right}
.wc-sem{color:var(--teal2);font-weight:700;font-size:.9rem}
.wc-date{color:rgba(255,255,255,.4);font-size:.78rem;margin-top:3px}

.tt{display:grid;border-radius:var(--r);overflow:hidden;border:1px solid var(--border);
  background:var(--white);box-shadow:var(--shadow-sm)}
.tt-hd{background:var(--ink);color:#fff;font-weight:700;font-size:.72rem;
  text-transform:uppercase;letter-spacing:.6px}
.tt-day{background:#f8fbff;font-weight:700;color:var(--ink);font-size:.8rem}
.tt-cell{padding:11px 9px;border-right:1px solid var(--border);border-bottom:1px solid var(--border);font-size:.78rem}
.tt-break{color:var(--slate2);font-style:italic;text-align:center;background:#fafcff}
.tt-period{color:#334155;line-height:1.3}

.chart-wrap{display:flex;align-items:flex-end;gap:8px;height:140px;padding:10px 0}
.chart-bar-g{display:flex;flex-direction:column;align-items:center;gap:4px;flex:1}
.chart-bar-outer{width:100%;background:var(--border);border-radius:4px 4px 0 0;
  display:flex;flex-direction:column;justify-content:flex-end;height:110px;overflow:hidden}
.chart-bar-fill{border-radius:4px 4px 0 0;transition:height .8s ease;min-height:4px}
.chart-lbl{font-size:.68rem;color:var(--slate2);font-weight:600}
.chart-val{font-size:.7rem;font-weight:700;color:var(--ink)}

.ab-wrap{background:var(--border);border-radius:4px;height:7px;overflow:hidden}
.ab-fill{height:100%;border-radius:4px;transition:width .7s ease}

.planner-grid{display:grid;grid-template-columns:repeat(5,1fr);gap:10px}
.plan-day{background:var(--white);border-radius:var(--r-sm);border:1px solid var(--border);overflow:hidden}
.plan-day-hd{background:var(--ink);color:#fff;font-size:.75rem;font-weight:700;
  text-align:center;padding:7px;letter-spacing:.5px;text-transform:uppercase}
.plan-slot{padding:8px 10px;border-bottom:1px solid var(--border2);font-size:.78rem;
  display:flex;flex-direction:column;gap:2px}
.plan-slot:last-child{border-bottom:none}
.plan-subj{font-weight:700;color:var(--ink);font-size:.8rem}
.plan-time{color:var(--slate2);font-size:.68rem}
.plan-type{display:inline-block;padding:2px 7px;border-radius:10px;font-size:.65rem;font-weight:700}

.uz{border:2px dashed var(--border);border-radius:var(--r);padding:32px;text-align:center;cursor:pointer;transition:all .2s}
.uz:hover{border-color:var(--teal);background:rgba(14,165,201,.04)}
.uz-icon{font-size:2.2rem;margin-bottom:10px}
.uz p{color:var(--slate);font-size:.88rem}.uz span{color:var(--teal);font-weight:700}

.qr-box{width:160px;height:160px;background:var(--ink);border-radius:var(--r);margin:0 auto 16px;
  display:flex;align-items:center;justify-content:center;font-size:4rem;position:relative;overflow:hidden}
.qr-box::after{content:'';position:absolute;inset:8px;border:3px solid rgba(14,165,201,.4);border-radius:8px;animation:qrpulse 2s infinite}
@keyframes qrpulse{0%,100%{opacity:.4;transform:scale(.95)}50%{opacity:1;transform:scale(1)}}
.qr-timer{background:#fee2e2;border-radius:var(--r-sm);padding:8px 16px;text-align:center;
  font-weight:700;color:var(--rose);font-size:1rem;margin-top:12px}

.risk-h{background:#fee2e2;color:var(--rose);padding:3px 10px;border-radius:20px;font-size:.71rem;font-weight:700}
.risk-m{background:#fef3c7;color:#92400e;padding:3px 10px;border-radius:20px;font-size:.71rem;font-weight:700}
.risk-l{background:#d1fae5;color:#065f46;padding:3px 10px;border-radius:20px;font-size:.71rem;font-weight:700}

.toast{position:fixed;bottom:26px;right:26px;background:var(--ink);color:#fff;padding:13px 20px;
  border-radius:var(--r-sm);font-size:.88rem;font-weight:500;box-shadow:var(--shadow-lg);
  z-index:9999;display:flex;align-items:center;gap:10px;animation:slideup .3s ease;
  border-left:3px solid var(--teal)}
@keyframes slideup{from{opacity:0;transform:translateY(20px)}to{opacity:1;transform:translateY(0)}}

.notif-panel{position:absolute;top:62px;right:16px;width:320px;background:var(--white);
  border-radius:var(--r);border:1px solid var(--border);box-shadow:var(--shadow-lg);z-index:200;overflow:hidden}
.notif-hd{padding:14px 16px;border-bottom:1px solid var(--border);display:flex;justify-content:space-between;align-items:center}
.notif-hd span{font-family:var(--font-d);font-size:.95rem;color:var(--ink)}
.notif-item{padding:12px 16px;border-bottom:1px solid var(--border2);display:flex;gap:10px;align-items:flex-start}
.notif-item:last-child{border-bottom:none}
.notif-item:hover{background:#f8fbff;cursor:pointer}
.notif-dot{width:8px;height:8px;border-radius:50%;flex-shrink:0;margin-top:4px}
.notif-msg{font-size:.82rem;color:#334155;line-height:1.4}
.notif-time{font-size:.7rem;color:var(--slate2);margin-top:3px}

.ach-badge{display:inline-flex;align-items:center;gap:5px;padding:5px 12px;
  border-radius:20px;font-size:.75rem;font-weight:700;margin:3px}

.g2{display:grid;grid-template-columns:1fr 1fr;gap:20px}
.g3{display:grid;grid-template-columns:1fr 1fr 1fr;gap:16px}
.g4{display:grid;grid-template-columns:repeat(4,1fr);gap:16px}
.mt14{margin-top:14px}.mt20{margin-top:20px}.mt26{margin-top:26px}
.mb14{margin-bottom:14px}.mb20{margin-bottom:20px}
.flex{display:flex}.fb{display:flex;align-items:center;justify-content:space-between}
.fc{display:flex;align-items:center}.gap10{gap:10px}.gap14{gap:14px}

.countdown{display:flex;gap:12px;justify-content:center;margin-top:12px}
.cd-block{background:var(--ink);color:#fff;border-radius:var(--r-sm);padding:12px 16px;text-align:center;min-width:64px}
.cd-num{font-family:var(--font-d);font-size:1.6rem;font-weight:800;line-height:1;color:var(--teal2)}
.cd-lbl{font-size:.65rem;text-transform:uppercase;letter-spacing:.8px;color:rgba(255,255,255,.4);margin-top:3px}

.health-card{background:var(--white);border-radius:var(--r);border:1px solid var(--border);
  padding:20px;box-shadow:var(--shadow-sm)}
.health-score{font-family:var(--font-d);font-size:2.2rem;font-weight:800}
.health-bar-row{display:flex;align-items:center;gap:10px;margin-bottom:8px;font-size:.8rem}
.health-bar-row .lb{width:60px;color:var(--slate);flex-shrink:0}
.health-bar-row .bw{flex:1}

.seat-grid{display:grid;gap:6px;margin-top:16px}
.seat-row{display:flex;gap:6px;flex-wrap:wrap}
.seat{width:36px;height:36px;border-radius:6px;display:flex;align-items:center;justify-content:center;
  font-size:.65rem;font-weight:700;border:1.5px solid var(--border)}
.seat-occ{background:var(--teal3);color:var(--teal);border-color:var(--teal)}
.seat-empty{background:#f8fbff;color:var(--slate2)}

.coding-platform{background:var(--white);border-radius:var(--r);border:1px solid var(--border);padding:20px;box-shadow:var(--shadow-sm)}
.platform-hd{display:flex;align-items:center;gap:10px;margin-bottom:16px}
.platform-logo{width:36px;height:36px;border-radius:9px;display:flex;align-items:center;justify-content:center;font-size:1.2rem}

.av-avail{background:#d1fae5;color:#065f46;font-size:.71rem;font-weight:700;padding:3px 8px;border-radius:20px}
.av-class{background:#dbeafe;color:#1e40af;font-size:.71rem;font-weight:700;padding:3px 8px;border-radius:20px}
.av-leave{background:#fee2e2;color:#991b1b;font-size:.71rem;font-weight:700;padding:3px 8px;border-radius:20px}
.av-dash{color:var(--slate2);font-size:.78rem}

.cert-card{background:linear-gradient(135deg,#fff9f0,#fff);border-radius:var(--r);
  border:2px solid var(--amber2);padding:30px;text-align:center;
  box-shadow:0 8px 32px rgba(245,158,11,.15);position:relative;overflow:hidden}
.cert-card::before{content:'';position:absolute;inset:4px;border:1px dashed var(--amber);border-radius:12px;pointer-events:none}
.cert-logo{font-size:3rem;margin-bottom:8px}
.cert-title{font-family:var(--font-d);font-size:1.4rem;color:var(--amber);margin-bottom:4px}
.cert-name{font-family:var(--font-d);font-size:1.8rem;color:var(--ink);margin:12px 0}
.cert-body{color:var(--slate);font-size:.88rem;line-height:1.7}

.ai-card{background:linear-gradient(135deg,#0d1b2a,#1b2e45);border-radius:var(--r);padding:22px;
  color:#fff;border:1px solid rgba(14,165,201,.25);box-shadow:0 8px 32px rgba(13,27,42,.3)}
.ai-card h4{font-family:var(--font-d);font-size:1rem;color:var(--teal2);margin-bottom:8px}
.ai-card p{color:rgba(255,255,255,.55);font-size:.83rem;line-height:1.6}
.ai-pred{font-size:2rem;font-weight:800;font-family:var(--font-d);color:var(--teal2);margin:10px 0}
.ai-tag{display:inline-block;padding:4px 12px;border-radius:20px;font-size:.72rem;font-weight:700;background:rgba(14,165,201,.2);color:var(--teal2);margin-top:6px}

.sotm{background:linear-gradient(135deg,#fef3c7,#fffbeb);border-radius:var(--r);
  border:2px solid var(--amber2);padding:22px;text-align:center;box-shadow:0 8px 24px rgba(245,158,11,.2)}
.sotm-trophy{font-size:3rem;margin-bottom:8px}
.sotm-name{font-family:var(--font-d);font-size:1.3rem;color:var(--ink)}
.sotm-detail{color:var(--slate);font-size:.83rem;margin-top:4px}

@media(max-width:768px){
  .sidebar{position:fixed;height:100vh;z-index:1000}
  .g2,.g3,.g4,.fr{grid-template-columns:1fr}
  .stats-grid{grid-template-columns:1fr 1fr}
  .wc{flex-direction:column;gap:12px}
  .planner-grid{grid-template-columns:1fr}
}
`;

const attC = p => p >= 75 ? "var(--green)" : "var(--rose)";
const attB = p => p >= 75 ? "b-g" : "b-r";
const today = new Date().toLocaleDateString("en-IN",{weekday:"long",day:"numeric",month:"long",year:"numeric"});

function Toast({msg,onClose}){
  useEffect(()=>{const t=setTimeout(onClose,3200);return()=>clearTimeout(t)},[]);
  return <div className="toast"><span>✅</span>{msg}</div>;
}
function StatCard({icon,value,label,sub,color,trend,trendUp}){
  return(
    <div className="stat-card">
      {trend&&<div className="sc-trend" style={{background:trendUp?"#d1fae5":"#fee2e2",color:trendUp?"#065f46":"#991b1b"}}>{trendUp?"↑":"↓"} {trend}</div>}
      <div className="sc-icon" style={{background:color+"18",color}}>{icon}</div>
      <div className="sc-val">{value}</div>
      <div className="sc-label">{label}</div>
      {sub&&<div className="sc-sub">{sub}</div>}
      <div style={{position:'absolute',right:-12,top:-12,width:60,height:60,borderRadius:'50%',background:color,opacity:.06}}/>
    </div>
  );
}
function Btn({children,onClick,variant="p",style}){
  return <button className={`btn-${variant}`} onClick={onClick} style={style}>{children}</button>;
}
function AttBar({pct}){
  return(
    <div style={{display:'flex',alignItems:'center',gap:10}}>
      <div className="ab-wrap" style={{flex:1}}><div className="ab-fill" style={{width:pct+'%',background:attC(pct)}}/></div>
      <span style={{minWidth:34,fontSize:'.8rem',fontWeight:700,color:attC(pct)}}>{pct}%</span>
    </div>
  );
}
function MiniChart({data,keyA,colorA}){
  const max = Math.max(...data.map(d=>d[keyA]||0));
  return(
    <div className="chart-wrap">
      {data.map((d,i)=>(
        <div key={i} className="chart-bar-g">
          <span className="chart-val">{d[keyA]}</span>
          <div className="chart-bar-outer">
            <div className="chart-bar-fill" style={{height:`${(d[keyA]/max)*90}%`,background:colorA}}/>
          </div>
          <span className="chart-lbl">{d.week}</span>
        </div>
      ))}
    </div>
  );
}
function SectionHeader({title,sub,action}){
  return(
    <div className="sh">
      <div><div className="section-title">{title}</div>{sub&&<div className="section-sub">{sub}</div>}</div>
      {action}
    </div>
  );
}
function Countdown(){
  const [time,setTime]=useState({d:12,h:4,m:32,s:18});
  useEffect(()=>{
    const t=setInterval(()=>setTime(p=>{
      let {d,h,m,s}=p;
      s--;if(s<0){s=59;m--}if(m<0){m=59;h--}if(h<0){h=23;d--}
      return{d,h,m,s};
    }),1000);return()=>clearInterval(t);
  },[]);
  return(
    <div className="countdown">
      {[{n:time.d,l:"Days"},{n:time.h,l:"Hours"},{n:time.m,l:"Mins"},{n:time.s,l:"Secs"}].map(x=>(
        <div key={x.l} className="cd-block"><div className="cd-num">{String(x.n).padStart(2,"0")}</div><div className="cd-lbl">{x.l}</div></div>
      ))}
    </div>
  );
}

function Sidebar({user,nav,active,onNav,col,onToggle}){
  return(
    <aside className={`sidebar${col?" col":""}`}>
      <div className="sb-head">
        <div className="sb-logo">🎓</div>
        {!col&&<div className="sb-title">EduPortal<span>Academic System</span></div>}
      </div>
      <div className="sb-user">
        <div className="sb-av">{user.avatar}</div>
        {!col&&<div className="sb-ui"><div className="sb-uname">{user.name}</div><div className="sb-urole">{user.role}</div></div>}
      </div>
      <nav className="sb-nav">
        {!col&&<div className="sb-section">Navigation</div>}
        {nav.map(item=>(
          <div key={item.id} className={`ni${active===item.id?" act":""}`} onClick={()=>onNav(item.id)} title={col?item.label:""}>
            <span className="ic">{item.icon}</span>
            {!col&&<span className="lb">{item.label}</span>}
          </div>
        ))}
      </nav>
      <div className="sb-bot">
        <button className="col-btn" onClick={onToggle}>
          <span className="ic">{col?"▶":"◀"}</span>
          {!col&&<span>Collapse Sidebar</span>}
        </button>
      </div>
    </aside>
  );
}

function Topbar({title,sub,user,onLogout,dark,onDark,onNotif}){
  return(
    <div className="topbar" style={{position:'relative'}}>
      <div className="tb-l"><h2>{title}</h2><p>{sub||today}</p></div>
      <div className="tb-r">
        <div className="tb-notif" onClick={onNotif}>🔔<div className="tb-notif-dot"/></div>
        <div className="tb-dark" onClick={onDark}>{dark?"☀️":"🌙"}</div>
        <div className="tb-badge">{user.role.toUpperCase()}</div>
        <button className="lo-btn" onClick={onLogout}>Sign Out</button>
      </div>
    </div>
  );
}

function AdminHome(){
  return(
    <div>
      <div className="wc">
        <div><h2>Admin Control Center</h2><p>Manage students, faculty, events, and institutional analytics.</p></div>
        <div className="wc-r"><div className="wc-sem">AY 2024–25</div><div className="wc-date">Even Semester · March 2025</div></div>
      </div>
      <div className="stats-grid">
        <StatCard icon="🎓" value="1,248" label="Total Students" sub="32 new this sem" color="var(--teal)" trend="2.6%" trendUp={true}/>
        <StatCard icon="👨‍🏫" value="86" label="Total Faculty" sub="12 departments" color="var(--purple)" trend="3" trendUp={true}/>
        <StatCard icon="📅" value="5" label="Upcoming Events" sub="Next: Mar 15" color="var(--amber)"/>
        <StatCard icon="⏳" value="3" label="Pending Approvals" sub="Requires action" color="var(--rose)"/>
        <StatCard icon="⚠️" value="12" label="Dropout Risk Students" sub="Needs attention" color="var(--rose)" trend="4" trendUp={false}/>
        <StatCard icon="🏛️" value="75" label="Avg Dept Health Score" sub="↑ from 71 last sem" color="var(--green)" trend="5.6%" trendUp={true}/>
      </div>
      <div className="g2 mb20">
        <div className="sotm">
          <div className="sotm-trophy">🏆</div>
          <div style={{fontSize:'.75rem',fontWeight:700,color:'var(--amber)',textTransform:'uppercase',letterSpacing:'.8px',marginBottom:4}}>Student of the Month</div>
          <div className="sotm-name">Sneha Pillai</div>
          <div className="sotm-detail">CS21B045 · 91% Attendance · CGPA 9.6</div>
          <div style={{marginTop:10,display:'flex',gap:6,justifyContent:'center',flexWrap:'wrap'}}>
            {["Perfect Attendance","Top 5 in Class","Assignment Streak"].map(b=>(
              <span key={b} className="ach-badge" style={{background:'#fef3c7',color:'#92400e'}}>🏅 {b}</span>
            ))}
          </div>
        </div>
        <div className="form-card" style={{marginBottom:0}}>
          <h3>📣 Recent Announcements</h3>
          {ANNOUNCEMENTS_HIST.slice(0,3).map((a,i)=>(
            <div key={i} style={{display:'flex',justifyContent:'space-between',alignItems:'center',padding:'8px 0',borderBottom:i<2?'1px solid var(--border2)':'none'}}>
              <div><div style={{fontSize:'.86rem',fontWeight:500,color:'var(--ink)'}}>{a.title}</div>
                <div style={{fontSize:'.72rem',color:'var(--slate2)',marginTop:2}}>{a.date}</div></div>
              <span className={`b ${a.target==='All'?'b-b':a.target==='Faculty'?'b-p':'b-t'}`}>{a.target}</span>
            </div>
          ))}
        </div>
      </div>
      <div className="g2">
        <div>
          <SectionHeader title="Student Risk Overview" sub="Dropout risk level distribution"/>
          <div className="tc"><table><thead><tr><th>Name</th><th>Roll</th><th>Att%</th><th>CGPA</th><th>Risk</th></tr></thead>
            <tbody>{STUDENTS.slice(0,5).map(s=>(
              <tr key={s.id}><td>{s.name}</td><td><code style={{fontSize:'.77rem'}}>{s.roll}</code></td>
                <td><span className={`b ${attB(s.att)}`}>{s.att}%</span></td><td><b>{s.cgpa}</b></td>
                <td><span className={`risk-${s.risk.toLowerCase()}`}>{s.risk}</span></td>
              </tr>
            ))}</tbody>
          </table></div>
        </div>
        <div>
          <SectionHeader title="Department Health Scores"/>
          {DEPT_HEALTH.map((d,i)=>(
            <div key={i} style={{marginBottom:14}}>
              <div className="fb mb14" style={{marginBottom:6}}>
                <span style={{fontSize:'.86rem',fontWeight:600,color:'var(--ink)'}}>{d.dept}</span>
                <span style={{fontFamily:'var(--font-d)',fontWeight:800,fontSize:'1rem',color:d.score>=75?'var(--green)':'var(--amber)'}}>{d.score}</span>
              </div>
              <AttBar pct={d.score}/>
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}

function ManageStudents({onToast}){
  const [tab,setTab]=useState("add");
  const [list,setList]=useState(STUDENTS);
  const [form,setForm]=useState({name:"",roll:"",dept:"",sem:"",email:"",phone:""});
  const [search,setSearch]=useState("");
  const h=e=>setForm({...form,[e.target.name]:e.target.value});
  const filtered=list.filter(s=>s.name.toLowerCase().includes(search.toLowerCase())||s.roll.toLowerCase().includes(search.toLowerCase()));
  return(
    <div>
      <div className="fc gap10 mb20">
        {["add","remove"].map(t=>(
          <button key={t} className={tab===t?"btn-p":"btn-s"} style={{marginLeft:0}} onClick={()=>setTab(t)}>
            {t==="add"?"➕ Add Student":"🗑️ Remove Student"}
          </button>
        ))}
      </div>
      {tab==="add"?(
        <div className="form-card">
          <h3>➕ Add New Student</h3>
          <div className="fr"><div className="form-group2"><label>Full Name</label><input name="name" value={form.name} onChange={h} placeholder="e.g. Arjun Krishnan"/></div>
            <div className="form-group2"><label>Roll Number</label><input name="roll" value={form.roll} onChange={h} placeholder="e.g. CS21B042"/></div></div>
          <div className="fr"><div className="form-group2"><label>Department</label><select name="dept" value={form.dept} onChange={h}><option value="">Select</option>{["CS","ME","ECE","Civil","EEE"].map(d=><option key={d}>{d}</option>)}</select></div>
            <div className="form-group2"><label>Semester</label><select name="sem" value={form.sem} onChange={h}><option value="">Select</option>{[1,2,3,4,5,6,7,8].map(s=><option key={s}>{s}</option>)}</select></div></div>
          <div className="fr"><div className="form-group2"><label>Email</label><input name="email" value={form.email} onChange={h} placeholder="student@edu.in"/></div>
            <div className="form-group2"><label>Phone</label><input name="phone" value={form.phone} onChange={h} placeholder="9876543210"/></div></div>
          <div style={{marginTop:8}}><Btn onClick={()=>onToast("Student added successfully!")}>Add Student</Btn><Btn variant="s" onClick={()=>setForm({name:"",roll:"",dept:"",sem:"",email:"",phone:""})}>Reset</Btn></div>
        </div>
      ):(
        <div>
          <div className="form-card" style={{marginBottom:16}}>
            <div className="fb"><h3 style={{marginBottom:0,paddingBottom:0,borderBottom:'none'}}>🗑️ Remove Students</h3>
              <input placeholder="Search name or roll…" style={{padding:'8px 12px',borderRadius:'var(--r-sm)',border:'1.5px solid var(--border)',fontFamily:'var(--font)',fontSize:'.87rem',width:230}} value={search} onChange={e=>setSearch(e.target.value)}/>
            </div>
          </div>
          <div className="tc"><table><thead><tr><th>Name</th><th>Roll</th><th>Dept</th><th>Sem</th><th>Att%</th><th>CGPA</th><th>Action</th></tr></thead>
            <tbody>{filtered.map(s=>(
              <tr key={s.id}><td>{s.name}</td><td><code style={{fontSize:'.77rem'}}>{s.roll}</code></td><td>{s.dept}</td>
                <td>Sem {s.sem}</td><td><span className={`b ${attB(s.att)}`}>{s.att}%</span></td><td><b>{s.cgpa}</b></td>
                <td><button className="btn-d" onClick={()=>{setList(l=>l.filter(x=>x.id!==s.id));onToast("Student removed.");}}>Remove</button></td>
              </tr>
            ))}</tbody>
          </table></div>
        </div>
      )}
    </div>
  );
}

function ManageFaculty({onToast}){
  const [tab,setTab]=useState("add");
  const [list,setList]=useState(FACULTY_LIST);
  const [form,setForm]=useState({name:"",empId:"",dept:"",subjects:"",email:"",qual:""});
  const h=e=>setForm({...form,[e.target.name]:e.target.value});
  return(
    <div>
      <div className="fc gap10 mb20">
        {["add","remove"].map(t=>(
          <button key={t} className={tab===t?"btn-p":"btn-s"} style={{marginLeft:0}} onClick={()=>setTab(t)}>
            {t==="add"?"➕ Add Faculty":"🗑️ Remove Faculty"}
          </button>
        ))}
      </div>
      {tab==="add"?(
        <div className="form-card">
          <h3>➕ Add New Faculty</h3>
          <div className="fr"><div className="form-group2"><label>Full Name</label><input name="name" value={form.name} onChange={h} placeholder="Prof. Name"/></div>
            <div className="form-group2"><label>Employee ID</label><input name="empId" value={form.empId} onChange={h} placeholder="FAC2025"/></div></div>
          <div className="fr"><div className="form-group2"><label>Department</label><select name="dept" value={form.dept} onChange={h}><option value="">Select</option>{["CS","ME","ECE","Civil","EEE"].map(d=><option key={d}>{d}</option>)}</select></div>
            <div className="form-group2"><label>Subjects</label><input name="subjects" value={form.subjects} onChange={h} placeholder="e.g. DBMS, OS"/></div></div>
          <div className="fr"><div className="form-group2"><label>Email</label><input name="email" value={form.email} onChange={h} placeholder="faculty@edu.in"/></div>
            <div className="form-group2"><label>Qualification</label><input name="qual" value={form.qual} onChange={h} placeholder="Ph.D / M.Tech"/></div></div>
          <Btn onClick={()=>onToast("Faculty added!")}>Add Faculty</Btn>
        </div>
      ):(
        <div className="tc"><table><thead><tr><th>Name</th><th>Emp ID</th><th>Dept</th><th>Subjects</th><th>Att%</th><th>Pass Rate</th><th>Action</th></tr></thead>
          <tbody>{list.map(f=>(
            <tr key={f.id}><td>{f.name}</td><td><code style={{fontSize:'.77rem'}}>{f.empId}</code></td><td>{f.dept}</td>
              <td style={{maxWidth:150}}>{f.subjects}</td>
              <td><span className={`b ${f.att>=75?"b-g":"b-r"}`}>{f.att}%</span></td>
              <td><b>{f.passRate}%</b></td>
              <td><button className="btn-d" onClick={()=>{setList(l=>l.filter(x=>x.id!==f.id));onToast("Faculty removed.");}}>Remove</button></td>
            </tr>
          ))}</tbody>
        </table></div>
      )}
    </div>
  );
}

function AdminTimetable(){
  return(
    <div>
      <div className="sh mb20"><div><div className="section-title">📅 Manage Timetables</div><div className="section-sub">Conflict detection enabled</div></div><Btn>+ Add Period</Btn></div>
      <div className="tc" style={{marginBottom:16,padding:'12px 16px',background:'#fff9f0',borderColor:'var(--amber2)'}}>
        <div style={{display:'flex',alignItems:'center',gap:8,fontSize:'.84rem',color:'#92400e'}}>⚠️ <b>Conflict Detected:</b> Networks Lab (Tuesday 11-1) overlaps with Algorithms for CS batch.</div>
      </div>
      <div className="tt" style={{gridTemplateColumns:'110px repeat(5,1fr)'}}>
        {["Day","9-10","10-11","Break","11-12","2-4"].map(h=><div key={h} className="tt-cell tt-hd">{h}</div>)}
        {TIMETABLE.map(r=>[
          <div key={r.day} className="tt-cell tt-day">{r.day}</div>,
          ...r.slots.map((s,i)=><div key={i} className={`tt-cell ${s==="BREAK"?"tt-break":"tt-period"}`}>{s}</div>)
        ])}
      </div>
    </div>
  );
}

function FacultyAttReport(){
  return(
    <div>
      <SectionHeader title="📈 Faculty Attendance Reports" sub="Current academic year"/>
      <div className="g2 mb20">
        <StatCard icon="✅" value="92%" label="Avg Faculty Attendance" sub="Across all departments" color="var(--green)"/>
        <StatCard icon="⚠️" value="2" label="Below 85% Threshold" sub="Requires HR attention" color="var(--rose)"/>
      </div>
      <div className="tc"><table><thead><tr><th>Name</th><th>Emp ID</th><th>Dept</th><th>Subjects</th><th>Attendance</th><th>Rating</th></tr></thead>
        <tbody>{FACULTY_LIST.map(f=>(
          <tr key={f.id}><td>{f.name}</td><td><code style={{fontSize:'.77rem'}}>{f.empId}</code></td><td>{f.dept}</td>
            <td>{f.subjects}</td><td style={{minWidth:160}}><AttBar pct={f.att}/></td>
            <td><span style={{fontFamily:'var(--font-d)',fontWeight:800,color:f.rating>=4.5?'var(--green)':f.rating>=4?'var(--teal)':'var(--amber)'}}>{f.rating} ⭐</span></td>
          </tr>
        ))}</tbody>
      </table></div>
    </div>
  );
}

function ManageActivities({onToast}){
  const [form,setForm]=useState({name:"",date:"",type:"",desc:""});
  const h=e=>setForm({...form,[e.target.name]:e.target.value});
  return(
    <div>
      <div className="form-card">
        <h3>🎉 Create Activity / Event</h3>
        <div className="fr"><div className="form-group2"><label>Event Name</label><input name="name" value={form.name} onChange={h} placeholder="e.g. Annual Tech Fest"/></div>
          <div className="form-group2"><label>Date</label><input type="date" name="date" value={form.date} onChange={h}/></div></div>
        <div className="fr fr1"><div className="form-group2"><label>Type</label><select name="type" value={form.type} onChange={h}><option value="">Select</option>{["Technical","Cultural","Sports","Academic","Other"].map(t=><option key={t}>{t}</option>)}</select></div></div>
        <div className="fr fr1"><div className="form-group2"><label>Description</label><textarea name="desc" value={form.desc} onChange={h} placeholder="Brief description…"/></div></div>
        <Btn onClick={()=>onToast("Event created!")}>Create Event</Btn>
      </div>
      <div className="tc"><table><thead><tr><th>Event</th><th>Date</th><th>Type</th><th>Participants</th><th>Status</th></tr></thead>
        <tbody>{ACTIVITIES.map(a=>(
          <tr key={a.id}><td>{a.name}</td><td>{a.date}</td><td><span className="b b-b">{a.type}</span></td>
            <td>{a.status==="Done"?a.part:"—"}</td>
            <td><span className={`b ${a.status==="Upcoming"?"b-b":a.status==="Pending"?"b-a":"b-g"}`}>{a.status}</span></td>
          </tr>
        ))}</tbody>
      </table></div>
    </div>
  );
}

function SendAnnouncements({onToast}){
  const [form,setForm]=useState({title:"",target:"All",msg:""});
  return(
    <div>
      <div className="form-card">
        <h3>📢 Send Announcement</h3>
        <div className="fr"><div className="form-group2"><label>Title</label><input value={form.title} onChange={e=>setForm({...form,title:e.target.value})} placeholder="Announcement title"/></div>
          <div className="form-group2"><label>Target Audience</label><select value={form.target} onChange={e=>setForm({...form,target:e.target.value})}><option>All</option><option>Students</option><option>Faculty</option></select></div></div>
        <div className="fr fr1"><div className="form-group2"><label>Message</label><textarea value={form.msg} onChange={e=>setForm({...form,msg:e.target.value})} placeholder="Write your message here…" style={{minHeight:110}}/></div></div>
        <Btn onClick={()=>onToast("Announcement sent!")}>📤 Send Now</Btn>
      </div>
      <SectionHeader title="Recent Announcements"/>
      <div className="tc"><table><thead><tr><th>Title</th><th>Target</th><th>Date</th></tr></thead>
        <tbody>{ANNOUNCEMENTS_HIST.map((a,i)=><tr key={i}><td>{a.title}</td><td><span className={`b ${a.target==="All"?"b-b":a.target==="Faculty"?"b-p":"b-t"}`}>{a.target}</span></td><td>{a.date}</td></tr>)}</tbody>
      </table></div>
    </div>
  );
}

function DeptHealthAnalytics(){
  return(
    <div>
      <SectionHeader title="🏛️ Department Health Analytics" sub="Composite score: Attendance + Marks + Activity"/>
      <div className="g3 mb20">
        {DEPT_HEALTH.map((d,i)=>(
          <div key={i} className="health-card">
            <div className="fb mb14">
              <span style={{fontFamily:'var(--font-d)',fontSize:'1rem',color:'var(--ink)'}}>{d.dept}</span>
              <div className="health-score" style={{color:d.score>=75?'var(--green)':d.score>=65?'var(--amber)':'var(--rose)'}}>{d.score}</div>
            </div>
            {[{lb:"Att",val:d.att,c:"var(--teal)"},{lb:"Marks",val:d.marks,c:"var(--purple)"},{lb:"Activity",val:d.activity,c:"var(--amber)"}].map(r=>(
              <div key={r.lb} className="health-bar-row">
                <span className="lb">{r.lb}</span>
                <div className="bw"><div className="ab-wrap"><div className="ab-fill" style={{width:r.val+'%',background:r.c}}/></div></div>
                <span style={{minWidth:30,fontSize:'.75rem',fontWeight:700,color:r.c,textAlign:'right'}}>{r.val}%</span>
              </div>
            ))}
          </div>
        ))}
      </div>
    </div>
  );
}

function DropoutRiskDash(){
  const high=STUDENTS.filter(s=>s.risk==="High");
  const medium=STUDENTS.filter(s=>s.risk==="Medium");
  return(
    <div>
      <SectionHeader title="⚠️ Student Dropout Risk Dashboard" sub="AI-flagged based on attendance + marks + activity"/>
      <div className="stats-grid mb20">
        <StatCard icon="🔴" value={high.length} label="High Risk" sub="Immediate action needed" color="var(--rose)"/>
        <StatCard icon="🟡" value={medium.length} label="Medium Risk" sub="Monitor closely" color="var(--amber)"/>
        <StatCard icon="🟢" value={STUDENTS.filter(s=>s.risk==="Low").length} label="Low Risk" sub="On track" color="var(--green)"/>
      </div>
      <div className="alert al-d">⚠️ <b>Action Required:</b> {high.length} students are at high dropout risk. Please schedule counselling sessions.</div>
      <div className="tc"><table><thead><tr><th>Name</th><th>Roll</th><th>Dept</th><th>Att%</th><th>CGPA</th><th>Risk</th><th>Action</th></tr></thead>
        <tbody>{STUDENTS.map(s=>(
          <tr key={s.id}><td>{s.name}</td><td><code style={{fontSize:'.77rem'}}>{s.roll}</code></td><td>{s.dept}</td>
            <td><span className={`b ${attB(s.att)}`}>{s.att}%</span></td><td><b>{s.cgpa}</b></td>
            <td><span className={`risk-${s.risk.toLowerCase()}`}>{s.risk}</span></td>
            <td><button className="btn-t">Schedule</button></td>
          </tr>
        ))}</tbody>
      </table></div>
    </div>
  );
}

function GenerateCertificates({onToast}){
  const [type,setType]=useState("Bonafide");
  const [name,setName]=useState("Arjun Krishnan");
  const [roll,setRoll]=useState("CS21B042");
  const [preview,setPreview]=useState(false);
  return(
    <div>
      <div className="form-card">
        <h3>📜 Certificate Generator</h3>
        <div className="fr"><div className="form-group2"><label>Certificate Type</label><select value={type} onChange={e=>setType(e.target.value)}><option>Bonafide</option><option>Attendance Certificate</option><option>Course Completion</option></select></div>
          <div className="form-group2"><label>Student Roll No.</label><input value={roll} onChange={e=>setRoll(e.target.value)}/></div></div>
        <div className="fr fr1"><div className="form-group2"><label>Student Name</label><input value={name} onChange={e=>setName(e.target.value)}/></div></div>
        <div style={{marginTop:8}}><Btn onClick={()=>setPreview(true)}>Preview Certificate</Btn><Btn variant="s" onClick={()=>onToast("Certificate downloaded!")}>Download PDF</Btn></div>
      </div>
      {preview&&(
        <div className="cert-card">
          <div className="cert-logo">🎓</div>
          <div style={{fontSize:'.75rem',fontWeight:700,color:'var(--slate)',textTransform:'uppercase',letterSpacing:'1px'}}>Excellence Institute of Technology</div>
          <div className="cert-title">{type}</div>
          <div style={{color:'var(--slate)',fontSize:'.86rem',margin:'8px 0'}}>This is to certify that</div>
          <div className="cert-name">{name}</div>
          <div className="cert-body">
            {type==="Bonafide"&&`bearing Roll Number ${roll} is a bonafide student of the B.Tech program in Computer Science and Engineering, currently studying in the 6th Semester of the academic year 2024–25.`}
            {type==="Attendance Certificate"&&`has maintained an attendance of 68% in the current semester. This certificate is issued as per the request of the student for official purposes.`}
            {type==="Course Completion"&&`has successfully completed all the academic requirements for the B.Tech degree program in Computer Science and Engineering with CGPA 8.4.`}
          </div>
          <div style={{marginTop:20,display:'flex',justifyContent:'space-between',fontSize:'.78rem',color:'var(--slate)'}}>
            <div>Date: {new Date().toLocaleDateString("en-IN")}</div>
            <div>Principal's Signature</div>
          </div>
        </div>
      )}
    </div>
  );
}

function ExamSeating({onToast}){
  const [students,setStudents]=useState(30);
  const [rooms,setRooms]=useState(2);
  const [generated,setGenerated]=useState(false);
  const perRoom=Math.ceil(students/rooms);
  return(
    <div>
      <div className="form-card">
        <h3>🪑 Exam Seating Arrangement Generator</h3>
        <div className="fr"><div className="form-group2"><label>Number of Students</label><input type="number" min={1} max={500} value={students} onChange={e=>setStudents(+e.target.value)}/></div>
          <div className="form-group2"><label>Number of Rooms</label><input type="number" min={1} max={20} value={rooms} onChange={e=>setRooms(+e.target.value)}/></div></div>
        <Btn onClick={()=>{setGenerated(true);onToast("Seating arrangement generated!");}}>Generate Seating Plan</Btn>
      </div>
      {generated&&Array.from({length:rooms},(_,ri)=>(
        <div key={ri} className="form-card" style={{marginBottom:16}}>
          <h3 style={{marginBottom:12,paddingBottom:8,borderBottom:'1px solid var(--border)'}}>🏫 Room {ri+1} — {Math.min(perRoom,students-ri*perRoom)} Students</h3>
          <div className="seat-grid">
            {Array.from({length:5},(_,row)=>(
              <div key={row} className="seat-row">
                {Array.from({length:Math.ceil(perRoom/5)},(_,col)=>{
                  const seatN=ri*perRoom+row*Math.ceil(perRoom/5)+col+1;
                  const occ=seatN<=students;
                  return <div key={col} className={`seat ${occ?"seat-occ":"seat-empty"}`}>{occ?seatN:"—"}</div>;
                })}
              </div>
            ))}
          </div>
        </div>
      ))}
    </div>
  );
}

function FacultyHome({user}){
  return(
    <div>
      <div className="wc">
        <div><h2>Welcome, {user.name}!</h2><p>{user.dept} Department · Employee ID: {user.empId}</p></div>
        <div className="wc-r"><div className="wc-sem">Even Semester 2024–25</div><div className="wc-date">March 2025</div></div>
      </div>
      <div className="stats-grid">
        <StatCard icon="📚" value="4" label="Classes Handled" sub="DBMS, OS, Networks, Algo" color="var(--teal)"/>
        <StatCard icon="✅" value="96%" label="My Attendance" sub="This semester" color="var(--green)" trend="2%" trendUp={true}/>
        <StatCard icon="📝" value="3" label="Pending Tasks" sub="Mark uploads, assignments" color="var(--amber)"/>
        <StatCard icon="📌" value="38" label="Assignments Submitted" sub="Out of 45 students" color="var(--purple)"/>
      </div>
      <div className="g2">
        <div>
          <SectionHeader title="Class Attendance Summary"/>
          <div className="tc"><table><thead><tr><th>Subject</th><th>Total</th><th>Avg Att%</th></tr></thead>
            <tbody>{[{s:"DBMS",t:45,a:74},{s:"OS",t:40,a:82},{s:"Networks",t:38,a:78},{s:"Algorithms",t:42,a:88}].map((r,i)=>(
              <tr key={i}><td>{r.s}</td><td>{r.t}</td><td><span className={`b ${attB(r.a)}`}>{r.a}%</span></td></tr>
            ))}</tbody>
          </table></div>
        </div>
        <div>
          <SectionHeader title="⏰ Exam Countdown"/>
          <div className="form-card" style={{textAlign:'center',marginBottom:0}}>
            <div style={{fontSize:'.8rem',color:'var(--slate)',textTransform:'uppercase',letterSpacing:'.6px',marginBottom:8}}>Next Internal Assessment (IA3)</div>
            <Countdown/>
            <div style={{marginTop:12,fontSize:'.82rem',color:'var(--slate)'}}>March 21, 2025 · 9:00 AM</div>
          </div>
        </div>
      </div>
    </div>
  );
}

function UploadNotes({onToast}){
  const [form,setForm]=useState({subject:"",unit:"",title:""});
  const h=e=>setForm({...form,[e.target.name]:e.target.value});
  return(
    <div className="form-card">
      <h3>📁 Upload Notes / Study Material</h3>
      <div className="fr"><div className="form-group2"><label>Subject</label><select name="subject" value={form.subject} onChange={h}><option value="">Select</option>{["DBMS","OS","Networks","Algorithms"].map(s=><option key={s}>{s}</option>)}</select></div>
        <div className="form-group2"><label>Unit</label><select name="unit" value={form.unit} onChange={h}><option value="">Select</option>{[1,2,3,4,5].map(u=><option key={u}>Unit {u}</option>)}</select></div></div>
      <div className="fr fr1"><div className="form-group2"><label>Title</label><input name="title" value={form.title} onChange={h} placeholder="e.g. ER Diagram & Normalization"/></div></div>
      <div className="uz mb14"><div className="uz-icon">📄</div><p>Drag & drop or <span>browse files</span></p><p style={{fontSize:'.75rem',color:'var(--slate2)',marginTop:4}}>PDF, PPTX, DOCX · Max 50MB</p></div>
      <Btn onClick={()=>onToast("Notes uploaded to Digital Library!")}>Upload Notes</Btn>
    </div>
  );
}

function UploadAttendance({onToast}){
  const [subj,setSubj]=useState("DBMS");
  const [att,setAtt]=useState(STUDENTS.reduce((a,s)=>({...a,[s.id]:true}),{}));
  return(
    <div>
      <div className="form-card mb14">
        <h3 style={{marginBottom:14,paddingBottom:10,borderBottom:'1px solid var(--border)'}}>📋 Upload Student Attendance</h3>
        <div className="fr"><div className="form-group2"><label>Subject</label><select value={subj} onChange={e=>setSubj(e.target.value)}><option>DBMS</option><option>OS</option><option>Networks</option><option>Algorithms</option></select></div>
          <div className="form-group2"><label>Date</label><input type="date" defaultValue="2025-03-09"/></div></div>
      </div>
      <div className="tc"><table><thead><tr><th>Roll</th><th>Name</th><th>Present</th><th>Absent</th></tr></thead>
        <tbody>{STUDENTS.filter(s=>s.dept==="CS").map(s=>(
          <tr key={s.id}><td><code style={{fontSize:'.77rem'}}>{s.roll}</code></td><td>{s.name}</td>
            <td><input type="radio" name={`a${s.id}`} checked={att[s.id]} onChange={()=>setAtt({...att,[s.id]:true})}/></td>
            <td><input type="radio" name={`a${s.id}`} checked={!att[s.id]} onChange={()=>setAtt({...att,[s.id]:false})}/></td>
          </tr>
        ))}</tbody>
      </table></div>
      <div className="mt14"><Btn onClick={()=>onToast("Attendance submitted!")}>Submit Attendance</Btn></div>
    </div>
  );
}

function UploadMarks({onToast}){
  const [form,setForm]=useState({subject:"",exam:""});
  return(
    <div>
      <div className="form-card mb14">
        <h3 style={{marginBottom:14,paddingBottom:10,borderBottom:'1px solid var(--border)'}}>📝 Upload Test Marks</h3>
        <div className="fr"><div className="form-group2"><label>Subject</label><select value={form.subject} onChange={e=>setForm({...form,subject:e.target.value})}><option value="">Select</option><option>DBMS</option><option>OS</option><option>Networks</option><option>Algorithms</option></select></div>
          <div className="form-group2"><label>Exam</label><select value={form.exam} onChange={e=>setForm({...form,exam:e.target.value})}><option value="">Select</option><option>IA1</option><option>IA2</option><option>IA3</option></select></div></div>
      </div>
      <div className="tc"><table><thead><tr><th>Roll</th><th>Name</th><th>Marks (out of 25)</th></tr></thead>
        <tbody>{STUDENTS.filter(s=>s.dept==="CS").map((s,i)=>(
          <tr key={s.id}><td><code style={{fontSize:'.77rem'}}>{s.roll}</code></td><td>{s.name}</td>
            <td><input type="number" min={0} max={25} defaultValue={18+Math.floor(i*1.2)} style={{width:70,padding:'5px 10px',border:'1.5px solid var(--border)',borderRadius:'var(--r-sm)',fontFamily:'var(--font)'}}/></td>
          </tr>
        ))}</tbody>
      </table></div>
      <div className="mt14"><Btn onClick={()=>onToast("Marks uploaded!")}>Upload Marks</Btn></div>
    </div>
  );
}

function FacultySendReminders({onToast}){
  const [form,setForm]=useState({type:"",msg:"",to:"All"});
  return(
    <div className="form-card">
      <h3>🔔 Send Reminders to Students</h3>
      <div className="fr"><div className="form-group2"><label>Reminder Type</label><select value={form.type} onChange={e=>setForm({...form,type:e.target.value})}><option value="">Select</option><option>Assignment Due</option><option>Exam Notice</option><option>Attendance Warning</option><option>General</option></select></div>
        <div className="form-group2"><label>Send To</label><select value={form.to} onChange={e=>setForm({...form,to:e.target.value})}><option>All Students</option><option>Low Attendance Students</option></select></div></div>
      <div className="fr fr1"><div className="form-group2"><label>Message</label><textarea value={form.msg} onChange={e=>setForm({...form,msg:e.target.value})} placeholder="Write your reminder here…" style={{minHeight:110}}/></div></div>
      <Btn onClick={()=>onToast("Reminder sent to students!")}>📤 Send Reminder</Btn>
    </div>
  );
}

function CreateAssignments({onToast}){
  const [form,setForm]=useState({subject:"",title:"",due:"",maxMarks:""});
  const h=e=>setForm({...form,[e.target.name]:e.target.value});
  return(
    <div>
      <div className="form-card">
        <h3>📌 Create New Assignment</h3>
        <div className="fr"><div className="form-group2"><label>Subject</label><select name="subject" value={form.subject} onChange={h}><option value="">Select</option><option>DBMS</option><option>OS</option><option>Networks</option><option>Algorithms</option></select></div>
          <div className="form-group2"><label>Max Marks</label><input name="maxMarks" value={form.maxMarks} onChange={h} placeholder="10"/></div></div>
        <div className="fr fr1"><div className="form-group2"><label>Title</label><input name="title" value={form.title} onChange={h} placeholder="e.g. ER Diagram Design"/></div></div>
        <div className="fr"><div className="form-group2"><label>Due Date</label><input type="date" name="due" value={form.due} onChange={h}/></div><div style={{flex:1}}/></div>
        <Btn onClick={()=>onToast("Assignment created!")}>Create Assignment</Btn>
      </div>
      <SectionHeader title="Existing Assignments"/>
      <div className="tc"><table><thead><tr><th>Title</th><th>Subject</th><th>Due</th><th>Submitted</th><th>Status</th></tr></thead>
        <tbody>{ASSIGNMENTS.map(a=>(
          <tr key={a.id}><td>{a.title}</td><td>{a.subject}</td><td>{a.due}</td>
            <td><b>{a.submitted}</b>/{a.total}</td>
            <td><span className={`b ${a.status==="Active"?"b-g":"b-s"}`}>{a.status}</span></td>
          </tr>
        ))}</tbody>
      </table></div>
    </div>
  );
}

function CheckLeaveStatus({onToast}){
  const [list,setList]=useState(LEAVES);
  function act(id,action){
    setList(l=>l.map(x=>x.id===id?{...x,status:action}:x));
    onToast(`Leave request ${action.toLowerCase()}!`);
  }
  return(
    <div>
      <SectionHeader title="🗒️ Student Leave / OD Requests" sub="Review and approve student applications"/>
      <div className="tc"><table><thead><tr><th>Student</th><th>Roll</th><th>From</th><th>To</th><th>Reason</th><th>Status</th><th>Action</th></tr></thead>
        <tbody>{list.map(l=>(
          <tr key={l.id}><td>{l.student}</td><td><code style={{fontSize:'.77rem'}}>{l.roll}</code></td>
            <td>{l.from}</td><td>{l.to}</td><td>{l.reason}</td>
            <td><span className={`b ${l.status==="Approved"?"b-g":l.status==="Rejected"?"b-r":"b-a"}`}>{l.status}</span></td>
            <td style={{display:'flex',gap:6}}>{l.status==="Pending"&&<><button className="btn-t" onClick={()=>act(l.id,"Approved")}>✅</button><button className="btn-d" onClick={()=>act(l.id,"Rejected")}>❌</button></>}</td>
          </tr>
        ))}</tbody>
      </table></div>
    </div>
  );
}

function ViewStudentList(){
  return(
    <div>
      <SectionHeader title="👥 Student List" sub="All enrolled students"/>
      <div className="tc"><table><thead><tr><th>Roll</th><th>Name</th><th>Dept</th><th>Sem</th><th>Attendance</th><th>CGPA</th></tr></thead>
        <tbody>{STUDENTS.map(s=>(
          <tr key={s.id}><td><code style={{fontSize:'.77rem'}}>{s.roll}</code></td><td>{s.name}</td><td>{s.dept}</td><td>Sem {s.sem}</td>
            <td><span className={`b ${attB(s.att)}`}>{s.att}%</span></td><td><b>{s.cgpa}</b></td>
          </tr>
        ))}</tbody>
      </table></div>
    </div>
  );
}

function AssignmentSubmissionChecker(){
  return(
    <div>
      <SectionHeader title="📋 Assignment Submission Checker"/>
      {ASSIGNMENTS.map(a=>(
        <div key={a.id} className="form-card" style={{marginBottom:16}}>
          <div className="fb mb14">
            <div><div style={{fontFamily:'var(--font-d)',fontSize:'1rem',color:'var(--ink)'}}>{a.title} <span className="b b-b" style={{marginLeft:6}}>{a.subject}</span></div>
              <div style={{fontSize:'.78rem',color:'var(--slate2)',marginTop:2}}>Due: {a.due}</div></div>
            <div style={{textAlign:'right'}}><div style={{fontFamily:'var(--font-d)',fontSize:'1.4rem',color:a.submitted/a.total>=.8?'var(--green)':'var(--amber)'}}>{a.submitted}/{a.total}</div></div>
          </div>
          <AttBar pct={Math.round(a.submitted/a.total*100)}/>
        </div>
      ))}
    </div>
  );
}

function PlagiarismResults(){
  const results=[
    {student:"Arjun Krishnan",  assignment:"ER Diagram",      similarity:12,status:"Clear"},
    {student:"Priya Nair",      assignment:"ER Diagram",      similarity:8, status:"Clear"},
    {student:"Rahul Varma",     assignment:"ER Diagram",      similarity:68,status:"Flagged"},
    {student:"Sneha Pillai",    assignment:"ER Diagram",      similarity:5, status:"Clear"},
    {student:"Karthik Bose",    assignment:"Process Scheduling",similarity:45,status:"Warning"},
  ];
  return(
    <div>
      <SectionHeader title="🔍 Plagiarism Detection Results" sub="AI-powered similarity analysis"/>
      <div className="alert al-i">ℹ️ Submissions with &gt;40% similarity are flagged.</div>
      <div className="tc"><table><thead><tr><th>Student</th><th>Assignment</th><th>Similarity %</th><th>Status</th></tr></thead>
        <tbody>{results.map((r,i)=>{
          const c=r.similarity>=60?'var(--rose)':r.similarity>=40?'var(--amber)':'var(--green)';
          return(<tr key={i}><td>{r.student}</td><td>{r.assignment}</td>
            <td><div style={{display:'flex',alignItems:'center',gap:10}}>
              <div className="ab-wrap" style={{flex:1,maxWidth:120}}><div className="ab-fill" style={{width:r.similarity+'%',background:c}}/></div>
              <span style={{fontWeight:700,color:c}}>{r.similarity}%</span></div></td>
            <td><span className={`b ${r.status==="Clear"?"b-g":r.status==="Warning"?"b-a":"b-r"}`}>{r.status}</span></td>
          </tr>);
        })}</tbody>
      </table></div>
    </div>
  );
}

function QRAttendance({onToast}){
  const [active,setActive]=useState(false);
  const [timer,setTimer]=useState(180);
  const [subj,setSubj]=useState("DBMS");
  useEffect(()=>{
    if(!active)return;
    if(timer<=0){setActive(false);setTimer(180);return;}
    const t=setTimeout(()=>setTimer(p=>p-1),1000);return()=>clearTimeout(t);
  },[active,timer]);
  const mm=String(Math.floor(timer/60)).padStart(2,"0");
  const ss=String(timer%60).padStart(2,"0");
  return(
    <div>
      <SectionHeader title="📲 QR Code Attendance System" sub="Students scan to mark attendance — auto-expires in 3 minutes"/>
      <div className="form-card" style={{textAlign:'center',maxWidth:360,margin:'0 auto'}}>
        <h3 style={{textAlign:'left'}}>Generate QR Code</h3>
        <div className="fr fr1"><div className="form-group2" style={{textAlign:'left'}}><label>Subject</label><select value={subj} onChange={e=>setSubj(e.target.value)}><option>DBMS</option><option>OS</option><option>Networks</option><option>Algorithms</option></select></div></div>
        {active?(
          <>
            <div className="qr-box">📱</div>
            <div style={{fontSize:'.82rem',color:'var(--slate)',marginBottom:6}}>Scan to mark attendance for <b>{subj}</b></div>
            <div className="qr-timer">⏱ Expires in: {mm}:{ss}</div>
            <button className="btn-d" style={{marginTop:12,width:'100%'}} onClick={()=>{setActive(false);setTimer(180);}}>Cancel QR</button>
          </>
        ):(
          <Btn onClick={()=>setActive(true)} style={{width:'100%',marginTop:8}}>🔲 Generate QR Code</Btn>
        )}
      </div>
    </div>
  );
}

function StudentHome({user}){
  return(
    <div>
      <div className="wc">
        <div><h2>Welcome, {user.name.split(" ")[0]}! 👋</h2><p>{user.dept} · Roll: {user.rollNo} · {user.sem} Semester</p></div>
        <div className="wc-r"><div className="wc-sem">AY 2024–25 Even</div><div className="wc-date">March 2025</div></div>
      </div>
      {user.attendance<75&&<div className="alert al-d">⚠️ <b>Attendance Warning:</b> Your attendance is {user.attendance}%, below the required 75%.</div>}
      <div className="stats-grid">
        <StatCard icon="📊" value={user.attendance+"%"} label="Overall Attendance" sub={user.attendance>=75?"Above minimum":"Below 75% threshold"} color={user.attendance>=75?"var(--green)":"var(--rose)"}/>
        <StatCard icon="⭐" value={user.cgpa} label="Current CGPA" sub="Sem 5 result" color="var(--amber)" trend="+0.2" trendUp={true}/>
        <StatCard icon="🏖️" value={user.leavesLeft} label="Leaves Remaining" sub="Out of 10 this sem" color="var(--teal)"/>
        <StatCard icon="💻" value={user.skillrack} label="SkillRack Programs" sub="Solved total" color="var(--purple)"/>
        <StatCard icon="⚡" value={user.leetcode} label="LeetCode Problems" sub="Solved total" color="var(--teal)"/>
        <StatCard icon="📚" value="5" label="Active Subjects" sub="Current semester" color="var(--ink2)"/>
      </div>
      <div className="g2">
        <div>
          <SectionHeader title="Subject Attendance"/>
          <div className="tc"><table><thead><tr><th>Subject</th><th>Present</th><th>Total</th><th>%</th></tr></thead>
            <tbody>{ATT_DETAIL.map((a,i)=><tr key={i}><td>{a.subject}</td><td>{a.present}</td><td>{a.total}</td>
              <td><span className={`b ${attB(a.pct)}`}>{a.pct}%</span></td></tr>)}</tbody>
          </table></div>
        </div>
        <div>
          <SectionHeader title="Achievements & Badges"/>
          <div className="form-card" style={{marginBottom:0}}>
            {[{b:"Assignment Streak",e:"🔥",c:"#fef3c7",t:"#92400e"},{b:"Active Coder",e:"💻",c:"#ede9fe",t:"#5b21b6"}].map(x=>(
              <span key={x.b} className="ach-badge" style={{background:x.c,color:x.t}}>{x.e} {x.b}</span>
            ))}
            <div className="mt14"><SectionHeader title="⏰ Next Exam Countdown" sub="IA3 · March 21, 2025"/><Countdown/></div>
          </div>
        </div>
      </div>
    </div>
  );
}

function ViewTimetable(){
  return(
    <div>
      <SectionHeader title="🗓️ Weekly Timetable" sub="Semester 6 · Computer Science"/>
      <div className="tt" style={{gridTemplateColumns:'100px repeat(5,1fr)'}}>
        {["Day","9-10 AM","10-11 AM","Break","11-12 PM","2-4 PM"].map(h=><div key={h} className="tt-cell tt-hd">{h}</div>)}
        {TIMETABLE.map(r=>[
          <div key={r.day} className="tt-cell tt-day">{r.day}</div>,
          ...r.slots.map((s,i)=><div key={i} className={`tt-cell ${s==="BREAK"?"tt-break":"tt-period"}`}>{s}</div>)
        ])}
      </div>
    </div>
  );
}

function ViewInternalMarks(){
  return(
    <div>
      <SectionHeader title="📝 Internal Assessment Marks" sub="Current Semester"/>
      <div className="tc"><table><thead><tr><th>Subject</th><th>IA1 (25)</th><th>IA2 (25)</th><th>IA3 (25)</th><th>Best 2 (50)</th><th>Status</th></tr></thead>
        <tbody>{INTERNAL_MARKS.map((m,i)=>{
          const best2=[m.ia1,m.ia2,m.ia3].sort((a,b)=>b-a).slice(0,2).reduce((a,b)=>a+b,0);
          return(<tr key={i}><td>{m.subject}</td><td>{m.ia1}</td><td>{m.ia2}</td><td>{m.ia3}</td>
            <td><b>{best2}/50</b></td><td><span className={`b ${best2>=25?"b-g":"b-r"}`}>{best2>=25?"Pass":"Fail"}</span></td></tr>);
        })}</tbody>
      </table></div>
      <div className="g2 mt20">
        <div className="ai-card"><h4>🤖 AI Grade Estimator</h4><p>Based on IA performance:</p><div className="ai-pred">CGPA Prediction: 8.6</div><span className="ai-tag">↑ +0.2 from current 8.4</span></div>
        <div className="ai-card"><h4>📊 Cohort Ranking</h4><p>Your standing in CS Sem 6 batch:</p><div className="ai-pred">Top 38%</div><span className="ai-tag">Rank ~17 out of 45</span></div>
      </div>
    </div>
  );
}

function ViewSemMarks(){
  const tot=SEM_MARKS.reduce((a,s)=>a+s.total,0);
  const maxT=SEM_MARKS.reduce((a,s)=>a+s.max,0);
  return(
    <div>
      <SectionHeader title="🎓 Semester Marks" sub="Semester 5 · Final Results"/>
      <div className="alert al-s">📊 Total: <b>{tot}/{maxT}</b> · Percentage: <b>{((tot/maxT)*100).toFixed(1)}%</b> · SGPA: <b>8.4</b></div>
      <div className="tc"><table><thead><tr><th>Subject</th><th>Internal (50)</th><th>External (100)</th><th>Total (150)</th><th>Grade</th></tr></thead>
        <tbody>{SEM_MARKS.map((m,i)=>(
          <tr key={i}><td>{m.subject}</td><td>{m.int}</td><td>{m.ext}</td><td><b>{m.total}</b></td>
            <td><span className={`b ${m.grade.includes("A")?"b-g":"b-b"}`}>{m.grade}</span></td></tr>
        ))}</tbody>
      </table></div>
    </div>
  );
}

function ApplyLeave({onToast}){
  const [form,setForm]=useState({type:"Leave",from:"",to:"",reason:""});
  const h=e=>setForm({...form,[e.target.name]:e.target.value});
  return(
    <div>
      <div className="stats-grid mb20">
        <StatCard icon="✅" value="7" label="Leaves Used" sub="Out of 10 total" color="var(--amber)"/>
        <StatCard icon="🏖️" value="3" label="Leaves Remaining" sub="Current semester" color="var(--teal)"/>
        <StatCard icon="🎗️" value="2" label="OD Availed" sub="This semester" color="var(--purple)"/>
      </div>
      <div className="form-card">
        <h3>📋 Apply for Leave / On Duty (OD)</h3>
        <div className="fr"><div className="form-group2"><label>Type</label><select name="type" value={form.type} onChange={h}><option>Leave</option><option>On Duty (OD)</option><option>Medical Leave</option></select></div><div style={{flex:1}}/></div>
        <div className="fr"><div className="form-group2"><label>From</label><input type="date" name="from" value={form.from} onChange={h}/></div>
          <div className="form-group2"><label>To</label><input type="date" name="to" value={form.to} onChange={h}/></div></div>
        <div className="fr fr1"><div className="form-group2"><label>Reason</label><textarea name="reason" value={form.reason} onChange={h} placeholder="Describe your reason…" style={{minHeight:100}}/></div></div>
        <Btn onClick={()=>onToast("Leave application submitted!")}>Submit Application</Btn>
      </div>
    </div>
  );
}

function DetailedAttendance(){
  return(
    <div>
      <SectionHeader title="📋 Period-wise Detailed Attendance"/>
      {ATT_DETAIL.map((sub,si)=>(
        <div key={si} style={{marginBottom:22}}>
          <div className="fb mb14" style={{marginBottom:8}}>
            <span style={{fontWeight:700,color:'var(--ink)',fontSize:'.95rem'}}>{sub.subject} <span style={{color:'var(--slate)',fontSize:'.8rem',fontWeight:400}}>({sub.present}/{sub.total})</span></span>
            <span className={`b ${attB(sub.pct)}`}>{sub.pct}%</span>
          </div>
          <div style={{display:'flex',gap:4,flexWrap:'wrap'}}>
            {Array.from({length:sub.total},(_,i)=>{
              const p=i<sub.present;
              return <div key={i} style={{width:26,height:26,borderRadius:5,background:p?'var(--green)':'var(--rose)',opacity:.85,display:'flex',alignItems:'center',justifyContent:'center',fontSize:'.62rem',color:'#fff',fontWeight:700}}>{i+1}</div>;
            })}
          </div>
        </div>
      ))}
    </div>
  );
}

function FacultyAvailability(){
  const days=["Mon","Tue","Wed","Thu","Fri"];
  const slots=["9-10","10-11","11-12","2-3","3-4"];
  return(
    <div>
      <SectionHeader title="👨‍🏫 Faculty Availability" sub="CS Department — current week"/>
      <div className="tc">
        <table><thead><tr><th>Faculty</th><th>Dept</th>{slots.map(s=><th key={s}>{s}</th>)}</tr></thead>
          <tbody>{FACULTY_AVAIL.map((f,fi)=>(
            <tr key={fi}><td>{f.name}</td><td>{f.dept}</td>
              {days.slice(0,5).map((d,di)=>{
                const val=f.slots[d]?.[di]||"—";
                return <td key={d}>{val==="Available"?<span className="av-avail">Available</span>:val==="In Class"?<span className="av-class">In Class</span>:val==="On Leave"?<span className="av-leave">On Leave</span>:<span className="av-dash">—</span>}</td>;
              })}
            </tr>
          ))}</tbody>
        </table>
      </div>
    </div>
  );
}

function SkillRackProgress(){
  const total=WEEKLY_CODING.reduce((a,w)=>a+w.skillrack,0);
  return(
    <div>
      <SectionHeader title="💻 SkillRack Weekly Progress"/>
      <div className="stats-grid mb20">
        <StatCard icon="🏆" value={total} label="Total Programs Solved" sub="All time" color="var(--purple)"/>
        <StatCard icon="📅" value={WEEKLY_CODING[WEEKLY_CODING.length-1].skillrack} label="This Week" sub="Programs solved" color="var(--teal)"/>
        <StatCard icon="📈" value="12" label="Class Average" sub="This week" color="var(--amber)"/>
        <StatCard icon="🥇" value="Top 35%" label="Batch Ranking" sub="Among 45 students" color="var(--green)"/>
      </div>
      <div className="form-card">
        <h3>Weekly Trend</h3>
        <MiniChart data={WEEKLY_CODING} keyA="skillrack" colorA="var(--purple)"/>
      </div>
    </div>
  );
}

function LeetCodeProgress(){
  const total=WEEKLY_CODING.reduce((a,w)=>a+w.leetcode,0);
  return(
    <div>
      <SectionHeader title="⚡ LeetCode Weekly Progress"/>
      <div className="stats-grid mb20">
        <StatCard icon="🏆" value={total} label="Total Problems Solved" sub="All time" color="var(--amber)"/>
        <StatCard icon="📅" value={WEEKLY_CODING[WEEKLY_CODING.length-1].leetcode} label="This Week" sub="Problems solved" color="var(--teal)"/>
        <StatCard icon="📈" value="5" label="Class Average" sub="This week" color="var(--purple)"/>
        <StatCard icon="🔥" value="6-week" label="Best Streak" sub="Consecutive" color="var(--green)"/>
      </div>
      <div className="form-card">
        <h3>Weekly Chart</h3>
        <MiniChart data={WEEKLY_CODING} keyA="leetcode" colorA="var(--amber)"/>
      </div>
    </div>
  );
}

function DigitalLibrary(){
  const [search,setSearch]=useState("");
  const [filter,setFilter]=useState("All");
  const filtered=LIBRARY_NOTES.filter(n=>(filter==="All"||n.subject===filter)&&(n.title.toLowerCase().includes(search.toLowerCase())||n.subject.toLowerCase().includes(search.toLowerCase())));
  return(
    <div>
      <SectionHeader title="📚 Digital Library" sub="Notes & study materials uploaded by faculty"/>
      <div className="fc gap10 mb20">
        <input placeholder="Search notes…" style={{flex:1,padding:'9px 13px',border:'1.5px solid var(--border)',borderRadius:'var(--r-sm)',fontFamily:'var(--font)',fontSize:'.88rem'}} value={search} onChange={e=>setSearch(e.target.value)}/>
        <select value={filter} onChange={e=>setFilter(e.target.value)} style={{padding:'9px 13px',border:'1.5px solid var(--border)',borderRadius:'var(--r-sm)',fontFamily:'var(--font)',fontSize:'.88rem'}}>
          <option>All</option><option>DBMS</option><option>OS</option><option>Networks</option><option>Algorithms</option><option>Maths III</option>
        </select>
      </div>
      <div className="g3">
        {filtered.map(n=>(
          <div key={n.id} className="form-card" style={{marginBottom:0,cursor:'pointer'}}>
            <div style={{display:'flex',alignItems:'flex-start',gap:10,marginBottom:10}}>
              <div style={{width:36,height:36,borderRadius:8,background:n.type==="PDF"?"#fee2e2":"#dbeafe",color:n.type==="PDF"?"var(--rose)":"var(--teal)",display:'flex',alignItems:'center',justifyContent:'center',fontSize:'1rem',flexShrink:0}}>
                {n.type==="PDF"?"📄":n.type==="PPTX"?"📊":"📝"}
              </div>
              <div><div style={{fontWeight:700,fontSize:'.87rem',color:'var(--ink)',lineHeight:1.3}}>{n.title}</div>
                <span className="b b-b" style={{marginTop:4,display:'inline-block',fontSize:'.68rem'}}>{n.subject}</span></div>
            </div>
            <div style={{fontSize:'.75rem',color:'var(--slate2)'}}>Unit: {n.unit} · By {n.faculty}</div>
            <button className="btn-t" style={{marginTop:10,width:'100%'}}>⬇ Download</button>
          </div>
        ))}
      </div>
    </div>
  );
}

function AssignmentPortal({onToast}){
  return(
    <div>
      <SectionHeader title="📤 Assignment Submission Portal"/>
      {ASSIGNMENTS.filter(a=>a.status==="Active").map(a=>(
        <div key={a.id} className="form-card">
          <div className="fb mb14">
            <div><div style={{fontFamily:'var(--font-d)',fontSize:'1rem',color:'var(--ink)'}}>{a.title}</div>
              <div style={{fontSize:'.78rem',color:'var(--slate2)',marginTop:2}}>{a.subject} · Due: <b>{a.due}</b></div></div>
          </div>
          <div className="uz"><div className="uz-icon">📎</div><p>Drag & drop your file or <span>browse</span></p></div>
          <div className="mt14"><Btn onClick={()=>onToast("Assignment submitted successfully!")}>Submit Assignment</Btn></div>
        </div>
      ))}
    </div>
  );
}

function StudyPlanner(){
  const plan={
    Mon:[{subj:"DBMS",time:"6-7 AM",type:"Revision",c:"#dbeafe",t:"#1e40af"},{subj:"Algorithms",time:"8-9 PM",type:"Practice",c:"#d1fae5",t:"#065f46"}],
    Tue:[{subj:"OS",time:"6-7 AM",type:"New Topic",c:"#fef3c7",t:"#92400e"},{subj:"Maths III",time:"8-9 PM",type:"Problems",c:"#ede9fe",t:"#5b21b6"}],
    Wed:[{subj:"Networks",time:"6-7 AM",type:"Revision",c:"#dbeafe",t:"#1e40af"},{subj:"LeetCode",time:"8-9 PM",type:"Practice",c:"#fee2e2",t:"#991b1b"}],
    Thu:[{subj:"DBMS Lab",time:"6-7 AM",type:"Practice",c:"#d1fae5",t:"#065f46"},{subj:"OS",time:"8-9 PM",type:"Revision",c:"#fef3c7",t:"#92400e"}],
    Fri:[{subj:"Mock Test",time:"6-8 AM",type:"Test",c:"#fee2e2",t:"#991b1b"},{subj:"SkillRack",time:"7-9 PM",type:"Coding",c:"#ede9fe",t:"#5b21b6"}],
  };
  return(
    <div>
      <div className="sh mb20"><div><div className="section-title">📅 AI-Personalized Study Planner</div><div className="section-sub">Generated based on weak subjects & upcoming exams</div></div></div>
      <div className="alert al-i">🤖 Plan generated based on your low scores in DBMS, OS, and upcoming IA3 on March 21.</div>
      <div className="planner-grid">
        {Object.entries(plan).map(([day,slots])=>(
          <div key={day} className="plan-day">
            <div className="plan-day-hd">{day}</div>
            {slots.map((s,i)=>(
              <div key={i} className="plan-slot">
                <span className="plan-subj">{s.subj}</span>
                <span className="plan-time">{s.time}</span>
                <span className="plan-type" style={{background:s.c,color:s.t}}>{s.type}</span>
              </div>
            ))}
          </div>
        ))}
      </div>
      <div className="ai-card mt20">
        <h4>🤖 AI Attendance Predictor</h4>
        <p>Based on current 68% attendance and remaining classes:</p>
        <div className="ai-pred">⚠️ Risk: Detained</div>
        <span className="ai-tag">Must attend 28 of next 35 classes</span>
      </div>
    </div>
  );
}

function AnonymousFeedback({onToast}){
  const [ratings,setRatings]=useState({});
  const [submitted,setSubmitted]=useState(false);
  return(
    <div>
      <SectionHeader title="📝 Anonymous Faculty Feedback"/>
      <div className="alert al-i">🔒 Your feedback is completely anonymous.</div>
      {!submitted?(
        <div>
          {FACULTY_LIST.slice(0,3).map(f=>(
            <div key={f.id} className="form-card">
              <h3>{f.name} <span style={{fontSize:'.78rem',color:'var(--slate)',fontFamily:'var(--font)',fontWeight:400}}>· {f.subjects}</span></h3>
              <div className="fr"><div className="form-group2"><label>Teaching Quality</label>
                <div className="fc gap10" style={{marginTop:4}}>
                  {[1,2,3,4,5].map(n=><span key={n} onClick={()=>setRatings({...ratings,[`${f.id}-q`]:n})} style={{fontSize:'1.5rem',cursor:'pointer',opacity:ratings[`${f.id}-q`]>=n?1:.3}}>⭐</span>)}
                </div></div>
                <div className="form-group2"><label>Approachability</label>
                  <div className="fc gap10" style={{marginTop:4}}>
                    {[1,2,3,4,5].map(n=><span key={n} onClick={()=>setRatings({...ratings,[`${f.id}-a`]:n})} style={{fontSize:'1.5rem',cursor:'pointer',opacity:ratings[`${f.id}-a`]>=n?1:.3}}>⭐</span>)}
                  </div></div></div>
            </div>
          ))}
          <Btn onClick={()=>{setSubmitted(true);onToast("Feedback submitted anonymously!");}}>Submit Anonymous Feedback</Btn>
        </div>
      ):(
        <div className="alert al-s" style={{fontSize:'1rem',padding:'20px'}}>✅ Thank you! Your feedback has been submitted anonymously.</div>
      )}
    </div>
  );
}

const ADMIN_NAV=[
  {id:"home",icon:"🏠",label:"Dashboard"},
  {id:"students",icon:"🎓",label:"Manage Students"},
  {id:"faculty",icon:"👨‍🏫",label:"Manage Faculty"},
  {id:"timetable",icon:"📅",label:"Timetable Manager"},
  {id:"fac-att",icon:"📈",label:"Faculty Attendance"},
  {id:"activities",icon:"🎉",label:"Activities & Events"},
  {id:"announce",icon:"📢",label:"Announcements"},
  {id:"analytics",icon:"📊",label:"Dept Health Analytics"},
  {id:"dropout",icon:"⚠️",label:"Dropout Risk Dashboard"},
  {id:"certs",icon:"📜",label:"Generate Certificates"},
  {id:"seating",icon:"🪑",label:"Exam Seating"},
];
const FACULTY_NAV=[
  {id:"home",icon:"🏠",label:"Dashboard"},
  {id:"notes",icon:"📁",label:"Upload Notes"},
  {id:"attendance",icon:"✅",label:"Upload Attendance"},
  {id:"marks",icon:"📝",label:"Upload Test Marks"},
  {id:"reminders",icon:"🔔",label:"Send Reminders"},
  {id:"assignments",icon:"📌",label:"Create Assignments"},
  {id:"leaves",icon:"🗒️",label:"Leave Requests"},
  {id:"students",icon:"👥",label:"View Student List"},
  {id:"submission",icon:"📋",label:"Submission Checker"},
  {id:"plagiarism",icon:"🔍",label:"Plagiarism Results"},
  {id:"qr",icon:"📲",label:"QR Attendance"},
];
const STUDENT_NAV=[
  {id:"home",icon:"🏠",label:"Dashboard"},
  {id:"timetable",icon:"🗓️",label:"View Timetable"},
  {id:"int-marks",icon:"📝",label:"Internal Marks"},
  {id:"sem-marks",icon:"🎓",label:"Semester Marks"},
  {id:"leave",icon:"📋",label:"Apply Leave / OD"},
  {id:"att-detail",icon:"📊",label:"Detailed Attendance"},
  {id:"fac-avail",icon:"👨‍🏫",label:"Faculty Availability"},
  {id:"skillrack",icon:"💻",label:"SkillRack Progress"},
  {id:"leetcode",icon:"⚡",label:"LeetCode Progress"},
  {id:"library",icon:"📚",label:"Digital Library"},
  {id:"submit",icon:"📤",label:"Submit Assignment"},
  {id:"planner",icon:"📅",label:"Study Planner"},
  {id:"feedback",icon:"📝",label:"Faculty Feedback"},
];

function NotifPanel({onClose}){
  const dotC={danger:"var(--rose)",info:"var(--teal)",warning:"var(--amber)",success:"var(--green)"};
  return(
    <div className="notif-panel">
      <div className="notif-hd"><span>🔔 Notifications</span><button onClick={onClose} style={{border:'none',background:'none',cursor:'pointer',color:'var(--slate)',fontSize:'1.2rem'}}>×</button></div>
      {NOTIFICATIONS.map(n=>(
        <div key={n.id} className="notif-item">
          <div className="notif-dot" style={{background:dotC[n.type]||'var(--slate)'}}/>
          <div><div className="notif-msg">{n.msg}</div><div className="notif-time">{n.time}</div></div>
        </div>
      ))}
    </div>
  );
}

function pageName(nav,id){return(nav.find(n=>n.id===id)||{label:"Dashboard"}).label;}

function DashShell({user,nav,renderPage,onLogout}){
  const [active,setActive]=useState("home");
  const [col,setCol]=useState(false);
  const [toast,setToast]=useState(null);
  const [dark,setDark]=useState(false);
  const [notif,setNotif]=useState(false);

  useEffect(()=>{
    document.body.classList.toggle("dark-mode",dark);
  },[dark]);

  return(
    <div className="dash">
      <Sidebar user={user} nav={nav} active={active} onNav={id=>{setActive(id);setNotif(false);}} col={col} onToggle={()=>setCol(c=>!c)}/>
      <div className="main">
        <div style={{position:'relative'}}>
          <Topbar title={pageName(nav,active)} sub={`${user.role.charAt(0).toUpperCase()+user.role.slice(1)} Portal`} user={user} onLogout={onLogout} dark={dark} onDark={()=>setDark(d=>!d)} onNotif={()=>setNotif(v=>!v)}/>
          {notif&&<NotifPanel onClose={()=>setNotif(false)}/>}
        </div>
        <div className="page">{renderPage(active,msg=>setToast(msg))}</div>
      </div>
      {toast&&<Toast msg={toast} onClose={()=>setToast(null)}/>}
    </div>
  );
}

function AdminDash({user,onLogout}){
  function renderPage(active,toast){
    switch(active){
      case "home": return <AdminHome/>;
      case "students": return <ManageStudents onToast={toast}/>;
      case "faculty": return <ManageFaculty onToast={toast}/>;
      case "timetable": return <AdminTimetable/>;
      case "fac-att": return <FacultyAttReport/>;
      case "activities": return <ManageActivities onToast={toast}/>;
      case "announce": return <SendAnnouncements onToast={toast}/>;
      case "analytics": return <DeptHealthAnalytics/>;
      case "dropout": return <DropoutRiskDash/>;
      case "certs": return <GenerateCertificates onToast={toast}/>;
      case "seating": return <ExamSeating onToast={toast}/>;
      default: return <AdminHome/>;
    }
  }
  return <DashShell user={user} nav={ADMIN_NAV} renderPage={renderPage} onLogout={onLogout}/>;
}

function FacultyDash({user,onLogout}){
  function renderPage(active,toast){
    switch(active){
      case "home": return <FacultyHome user={user}/>;
      case "notes": return <UploadNotes onToast={toast}/>;
      case "attendance": return <UploadAttendance onToast={toast}/>;
      case "marks": return <UploadMarks onToast={toast}/>;
      case "reminders": return <FacultySendReminders onToast={toast}/>;
      case "assignments": return <CreateAssignments onToast={toast}/>;
      case "leaves": return <CheckLeaveStatus onToast={toast}/>;
      case "students": return <ViewStudentList/>;
      case "submission": return <AssignmentSubmissionChecker/>;
      case "plagiarism": return <PlagiarismResults/>;
      case "qr": return <QRAttendance onToast={toast}/>;
      default: return <FacultyHome user={user}/>;
    }
  }
  return <DashShell user={user} nav={FACULTY_NAV} renderPage={renderPage} onLogout={onLogout}/>;
}

function StudentDash({user,onLogout}){
  function renderPage(active,toast){
    switch(active){
      case "home": return <StudentHome user={user}/>;
      case "timetable": return <ViewTimetable/>;
      case "int-marks": return <ViewInternalMarks/>;
      case "sem-marks": return <ViewSemMarks/>;
      case "leave": return <ApplyLeave onToast={toast}/>;
      case "att-detail": return <DetailedAttendance/>;
      case "fac-avail": return <FacultyAvailability/>;
      case "skillrack": return <SkillRackProgress/>;
      case "leetcode": return <LeetCodeProgress/>;
      case "library": return <DigitalLibrary/>;
      case "submit": return <AssignmentPortal onToast={toast}/>;
      case "planner": return <StudyPlanner/>;
      case "feedback": return <AnonymousFeedback onToast={toast}/>;
      default: return <StudentHome user={user}/>;
    }
  }
  return <DashShell user={user} nav={STUDENT_NAV} renderPage={renderPage} onLogout={onLogout}/>;
}

function LoginPage({onLogin}){
  const [role,setRole]=useState("admin");
  const [email,setEmail]=useState("");
  const [pass,setPass]=useState("");
  const [err,setErr]=useState("");
  const roles=[{id:"admin",icon:"🛡️",label:"Admin"},{id:"faculty",icon:"👨‍🏫",label:"Faculty"},{id:"student",icon:"🎓",label:"Student"}];
  const hints={admin:"admin@edu.in / admin123",faculty:"faculty@edu.in / faculty123",student:"student@edu.in / student123"};
  function doLogin(){
    const u=USERS[role];
    if(email===u.email&&pass===u.password){setErr("");onLogin(u);}
    else setErr("Invalid credentials. Please try again.");
  }
  return(
    <div className="lp">
      <div className="lp-orb lp-orb1"/><div className="lp-orb lp-orb2"/>
      <div className="lp-wrap">
        <div className="lp-brand">
          <div className="lp-logo">🎓</div>
          <h1>EduPortal</h1>
          <p>Integrated Academic Management System</p>
        </div>
        <div className="role-tabs">
          {roles.map(r=><div key={r.id} className={`role-tab${role===r.id?" active":""}`} onClick={()=>{setRole(r.id);setErr("");setEmail("");setPass("");}}>
            <div className="ri">{r.icon}</div><div className="rn">{r.label}</div></div>)}
        </div>
        <div className="lp-form">
          <h2>{roles.find(r=>r.id===role).icon} {roles.find(r=>r.id===role).label} Login</h2>
          <div className="fg"><label>Email / Username</label><input type="email" placeholder="Enter your email" value={email} onChange={e=>setEmail(e.target.value)}/></div>
          <div className="fg"><label>Password</label><input type="password" placeholder="Enter password" value={pass} onChange={e=>setPass(e.target.value)} onKeyDown={e=>e.key==="Enter"&&doLogin()}/></div>
          <button className="lp-btn" onClick={doLogin}>Sign In →</button>
          {err&&<p className="lp-err">⚠️ {err}</p>}
          <p className="lp-hint">Demo: {hints[role]}</p>
        </div>
      </div>
    </div>
  );
}

export default function App(){
  const [user,setUser]=useState(null);
  return(
    <>
      <style>{CSS}</style>
      {!user&&<LoginPage onLogin={setUser}/>}
      {user?.role==="admin"&&<AdminDash user={user} onLogout={()=>{setUser(null);document.body.classList.remove("dark-mode");}}/>}
      {user?.role==="faculty"&&<FacultyDash user={user} onLogout={()=>{setUser(null);document.body.classList.remove("dark-mode");}}/>}
      {user?.role==="student"&&<StudentDash user={user} onLogout={()=>{setUser(null);document.body.classList.remove("dark-mode");}}/>}
    </>
  );
}
