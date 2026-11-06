```html
<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام أبراج الضياء المتكامل لإدارة المشاريع | ADY PMS</title>

    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>

    <!-- React & ReactDOM (Production UMD) -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>

    <!-- Babel Standalone (Compiles JSX in browser) -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

    <!-- Google Fonts: Cairo & Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;800;900&family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">

    <style>
        body {
            font-family: 'Cairo', 'Inter', sans-serif;
        }
        /* Printable Styles Optimization */
        @media print {
            aside, header, button, .no-print {
                display: none !important;
            }
            main {
                padding: 0 !important;
                margin: 0 !important;
            }
        }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 antialiased">

    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect, useMemo } = React;

        // --- INITIAL DATA REPOSITORY ---
        const INITIAL_PROJECTS = [
          {
            id: 'ADY-SOLAR-01',
            code: '1220-1221',
            nameAr: 'توريد وتركيب منظومات الطاقة الشمسية لمعامل ثلج القشور (5 طن/يوم)',
            nameEn: 'Supply & Installation of Solar Power Systems for 5-TPD Flake Ice Plants',
            client: 'UNDP / السلطات المحلية',
            contractor: 'شركة أبراج الضياء التجارية المحدودة (ADY)',
            projectManager: 'م. أحمد الضبيعي',
            locations: ['روكب (كود 1220)', 'الحامي (كود 1221)'],
            startDate: '2026-07-02',
            originalCompletion: '2026-11-20',
            revisedCompletion: '2027-02-25',
            extensionRequest: '2.5 شهر (تحت الاعتماد الرسمي)',
            status: 'قيد التنفيذ',
            progress: 42.5,
            health: 'تحت المتابعة (تأخر غرف التحكم)',
            contractValue: '$485,000',
            description: 'تصميم وتوريد وتركيب منظومات طاقة شمسية هجينة لمعامل ثلج القشور في موقعي روكب والحامي.'
          },
          {
            id: 'ADY-WATER-02',
            code: '2205',
            nameAr: 'محطة تحلية المياه بالطاقة الشمسية - الشحر',
            nameEn: 'Solar Powered RO Water Desalination Plant - Al-Shihr',
            client: 'مؤسسة المياه والصرف الصحي',
            contractor: 'شركة أبراج الضياء التجارية المحدودة (ADY)',
            projectManager: 'م. سالم باوزير',
            locations: ['ساحل الشحر'],
            startDate: '2026-01-10',
            originalCompletion: '2026-08-30',
            revisedCompletion: '2026-09-15',
            extensionRequest: 'لا يوجد',
            status: 'مكتمل',
            progress: 100,
            health: 'ممتاز',
            contractValue: '$320,000',
            description: 'تنفيذ كلي لمحطة تحلية مياه بالتناضح العكسي تعمل بالكامل بالطاقة الشمسية.'
          }
        ];

        const INITIAL_WBS = [
          { id: 'WBS-1', code: '1.0', nameAr: 'التجهيزات المسبقة والتصميم الهندسي', nameEn: 'Mobilization & Engineering Design', weight: 10, progress: 100, status: 'مكتمل', site: 'جميع المواقع' },
          { id: 'WBS-2', code: '2.0', nameAr: 'توريدات المعدات الرئيسية (ألواح، بطاريات، محولات)', nameEn: 'Procurement of Major Equipment', weight: 35, progress: 65, status: 'قيد التنفيذ', site: 'المخازن / الشحن' },
          { id: 'WBS-3', code: '3.0', nameAr: 'الأعمال المدنية والهياكل المعدنية (روكب والحامي)', nameEn: 'Civil Works & Steel Structures', weight: 15, progress: 80, status: 'قيد التنفيذ', site: 'روكب والحامي' },
          { id: 'WBS-4', code: '4.0', nameAr: 'أعمال الغرف الكهربائية (نطاق UNDP)', nameEn: 'Control Rooms Construction (UNDP Scope)', weight: 15, progress: 30, status: 'متأخر', site: 'روكب والحامي' },
          { id: 'WBS-5', code: '5.0', nameAr: 'التركيبات الكهربائية وربط المنظومة', nameEn: 'Electrical Installation & Cabling', weight: 15, progress: 0, status: 'لم يبدأ', site: 'روكب والحامي' },
          { id: 'WBS-6', code: '6.0', nameAr: 'الاختبار والتشغيل والتسليم النهائي', nameEn: 'Testing, Commissioning & Handover', weight: 10, progress: 0, status: 'لم يبدأ', site: 'روكب والحامي' },
        ];

        const INITIAL_ACTIVITIES = [
          { id: 'ACT-101', wbsCode: '1.0', nameAr: 'دراسة الموقع والتصميم الهندسي المبدئي', nameEn: 'Site Survey & Engineering Design', duration: 15, start: '2026-07-02', finish: '2026-07-20', progress: 100, status: 'مكتمل', responsible: 'الفريق الهندسي' },
          { id: 'ACT-102', wbsCode: '2.0', nameAr: 'توريد الهياكل المعدنية وفحص الجودة', nameEn: 'Steel Structures Supply & Fabrication', duration: 34, start: '2026-07-28', finish: '2026-08-30', progress: 100, status: 'مكتمل', responsible: 'إدارة المشتريات' },
          { id: 'ACT-103', wbsCode: '2.0', nameAr: 'توريد الألواح الشمسية (من الصين)', nameEn: 'Solar Panels Procurement & Shipment', duration: 36, start: '2026-08-05', finish: '2026-09-10', progress: 100, status: 'مكتمل', responsible: 'سلسلة التوريد' },
          { id: 'ACT-104', wbsCode: '2.0', nameAr: 'توريد البطاريات والمحولات (شحن وتصريح ليثيوم)', nameEn: 'Batteries & Inverters Shipment (Lithium Auth)', duration: 122, start: '2026-08-15', finish: '2026-12-15', progress: 75, status: 'قيد التنفيذ', responsible: 'سلسلة التوريد' },
          { id: 'ACT-105', wbsCode: '3.0', nameAr: 'تركيب الهياكل المعدنية الأرضية - موقع روكب', nameEn: 'Rokab Ground-Mounted Steel Structures', duration: 22, start: '2026-10-03', finish: '2026-10-25', progress: 100, status: 'مكتمل', responsible: 'فريق موقع روكب' },
          { id: 'ACT-106', wbsCode: '3.0', nameAr: 'تركيب الهياكل المعدنية وحفر الكابلات - الحامي', nameEn: 'Al-Hami Steel Structure & Cable Trenches', duration: 17, start: '2026-11-20', finish: '2026-12-07', progress: 60, status: 'قيد التنفيذ', responsible: 'فريق موقع الحامي' },
          { id: 'ACT-107', wbsCode: '4.0', nameAr: 'إنشاء الغرف الكهربائية والتحكم (UNDP Scope)', nameEn: 'Control Rooms Construction by UNDP', duration: 61, start: '2026-11-20', finish: '2027-01-20', progress: 30, status: 'متأخر', responsible: 'UNDP / المالك' },
          { id: 'ACT-108', wbsCode: '5.0', nameAr: 'التركيبات الكهربائية بالموقعين (بعد الاستلام)', nameEn: 'Electrical Installation at Both Sites', duration: 17, start: '2027-01-21', finish: '2027-02-07', progress: 0, status: 'لم يبدأ', responsible: 'الفريق الكهربائي' },
          { id: 'ACT-109', wbsCode: '6.0', nameAr: 'الفحص والتشغيل التجريبي للمنظومة', nameEn: 'Testing and Commissioning', duration: 7, start: '2027-02-03', finish: '2027-02-10', progress: 0, status: 'لم يبدأ', responsible: 'فريق التشغيل' },
          { id: 'ACT-110', wbsCode: '6.0', nameAr: 'التسليم النهائي وإعداد وثائق التشغيل والتدريب', nameEn: 'Handover Documentation and Training', duration: 6, start: '2027-02-11', finish: '2027-02-17', progress: 0, status: 'لم يبدأ', responsible: 'مدير المشروع' }
        ];

        const INITIAL_PROCUREMENT = [
          { id: 'PROC-01', itemAr: 'الألواح الشمسية (Solar Panels)', itemEn: 'Solar Panels (Monocrystalline)', supplier: 'مُصنع رئيسي (الصين)', po: 'PO-ADY-2026-001', qty: '450 لوح', contractualDate: '2026-09-10', plannedDate: '2026-09-10', actualDate: '2026-09-08', status: 'تم التسليم والملاحظة' },
          { id: 'PROC-02', itemAr: 'البطاريات ومحولات الطاقة (Batteries & Inverters)', itemEn: 'Lithium Batteries & Hybrid Inverters', supplier: 'Shenzhen Power Tech', po: 'PO-ADY-2026-002', qty: '20 وحدة', contractualDate: '2026-11-05', plannedDate: '2026-12-15', actualDate: 'في الطريق', status: 'قيد الشحن (تأخير تصاريح التصدير)' },
          { id: 'PROC-03', itemAr: 'الهياكل المعدنية المصنعة (Steel Structures)', itemEn: 'Galvanized Steel Structures', supplier: 'ورش تصنيع محلي وإقليمي', po: 'PO-ADY-2026-003', qty: '12 طن', contractualDate: '2026-08-04', plannedDate: '2026-08-04', actualDate: '2026-08-02', status: 'تم التوريد والاعتماد' }
        ];

        const INITIAL_RISKS = [
          { id: 'RSK-01', titleAr: 'تأخر استلام الغرف الكهربائية من UNDP', titleEn: 'Delay in Control Rooms Handover from UNDP', category: 'خارجي / المالك', prob: 'عالي', impact: 'عالي', score: 'حرج', owner: 'مدير المشروع', status: 'معالجة جارية' },
          { id: 'RSK-02', titleAr: 'تأخير تصاريح شحن بطاريات الليثيوم', titleEn: 'Lithium Battery Export/Import Regulations Delay', category: 'شحن ولوجستيات', prob: 'متوسط', impact: 'عالي', score: 'مرتفع', owner: 'مسؤول المشتريات', status: 'تحت المتابعة' }
        ];

        const TRANSLATIONS = {
          ar: {
            systemTitle: 'نظام أبراج الضياء المتكامل لإدارة المشاريع',
            companyName: 'شركة أبراج الضياء التجارية المحدودة (ADY)',
            dashboard: 'لوحة التحكم التنفيذية',
            portfolio: 'محفظة المشاريع',
            planning: 'التخطيط الزمني',
            gantt: 'المخطط الزمني (Gantt)',
            progress: 'متابعة الإنجاز',
            procurement: 'إدارة التوريدات',
            site: 'التنفيذ الميداني',
            resources: 'إدارة الموارد',
            risks: 'إدارة المخاطر',
            issues: 'إدارة المشكلات',
            delays: 'الانحرافات والتأخيرات',
            changes: 'إدارة التغييرات',
            documents: 'إدارة المستندات',
            reports: 'مركز التقارير',
            notifications: 'مركز التنبيهات',
            communication: 'مركز التواصل',
            users: 'المستخدمون والصلاحيات',
            settings: 'إعدادات النظام',
            importExport: 'الاستيراد والتصدير',
            searchPlaceholder: 'بحث عام في المشاريع والأنشطة...',
            activeProject: 'المشروع الحالي:',
            langToggle: 'English',
            whatsappBtn: 'التواصل مع مسؤول المشروع',
            totalProjects: 'إجمالي المشاريع',
            activeProjects: 'المشاريع النشطة',
            plannedProgress: 'الإنجاز المخطط',
            actualProgress: 'الإنجاز الفعلي',
            scheduleVariance: 'انحراف الجدول',
            procurementStatus: 'حالة التوريدات',
            openRisks: 'المخاطر المفتوحة',
            openIssues: 'المشكلات القائمة',
            saveData: 'حفظ التغييرات',
            exportExcel: 'تصدير Excel',
            printReport: 'طباعة التقرير',
            addNew: 'إضافة جديد',
            status: 'الحالة',
            delayReason: 'السبب الرئيسي للتأخير: تأخر جاهزية الغرف الكهربائية (نطاق UNDP) بالإضافة إلى تأخر تراخيص شحن بطاريات الليثيوم.'
          },
          en: {
            systemTitle: 'ADY Integrated Project Management System',
            companyName: 'Abrag AlDiyaa Trading Co. Ltd. (ADY)',
            dashboard: 'Executive Dashboard',
            portfolio: 'Projects Portfolio',
            planning: 'Project Planning',
            gantt: 'Gantt Chart',
            progress: 'Progress Monitoring',
            procurement: 'Procurement Management',
            site: 'Site Execution',
            resources: 'Resource Management',
            risks: 'Risk Management',
            issues: 'Issue Management',
            delays: 'Delay & Variance Analysis',
            changes: 'Change Management',
            documents: 'Document Management',
            reports: 'Reports Center',
            notifications: 'Notifications Center',
            communication: 'Communication Center',
            users: 'Users & Permissions',
            settings: 'System Settings',
            importExport: 'Import & Export',
            searchPlaceholder: 'Global search in projects, activities...',
            activeProject: 'Active Project:',
            langToggle: 'العربية',
            whatsappBtn: 'Contact Project Manager',
            totalProjects: 'Total Projects',
            activeProjects: 'Active Projects',
            plannedProgress: 'Planned Progress',
            actualProgress: 'Actual Progress',
            scheduleVariance: 'Schedule Variance',
            procurementStatus: 'Procurement Status',
            openRisks: 'Open Risks',
            openIssues: 'Open Issues',
            saveData: 'Save Changes',
            exportExcel: 'Export Excel',
            printReport: 'Print Report',
            addNew: 'Add New',
            status: 'Status',
            delayReason: 'Primary Delay Reason: Unreadiness of electrical control rooms (UNDP scope) and lithium battery export authorization delays.'
          }
        };

        function App() {
          const [lang, setLang] = useState('ar');
          const [activeTab, setActiveTab] = useState('dashboard');
          const [selectedProjectId, setSelectedProjectId] = useState('ADY-SOLAR-01');
          const [sidebarOpen, setSidebarOpen] = useState(true);
          const [toastMessage, setToastMessage] = useState('');

          const [projects] = useState(INITIAL_PROJECTS);
          const [wbsList, setWbsList] = useState(INITIAL_WBS);
          const [activities] = useState(INITIAL_ACTIVITIES);
          const [procurement] = useState(INITIAL_PROCUREMENT);
          const [risks] = useState(INITIAL_RISKS);

          const showToast = (msg) => {
            setToastMessage(msg);
            setTimeout(() => setToastMessage(''), 4000);
          };

          const t = TRANSLATIONS[lang];
          const isAr = lang === 'ar';
          const currentProject = projects.find(p => p.id === selectedProjectId) || projects[0];

          const calculatedProgress = useMemo(() => {
            let totalWeight = wbsList.reduce((acc, item) => acc + item.weight, 0);
            if (totalWeight === 0) return 0;
            let weightedSum = wbsList.reduce((acc, item) => acc + (item.weight * item.progress), 0);
            return (weightedSum / totalWeight).toFixed(1);
          }, [wbsList]);

          return (
            <div dir={isAr ? 'rtl' : 'ltr'} className="min-h-screen bg-slate-100 text-slate-800">
              
              {/* Toast Notification */}
              {toastMessage && (
                <div className="fixed bottom-6 z-50 left-1/2 transform -translate-x-1/2 bg-slate-900 text-white px-6 py-3 rounded-xl shadow-2xl border border-red-500 flex items-center gap-3 animate-bounce">
                  <span className="w-3 h-3 bg-green-500 rounded-full inline-block"></span>
                  <span>{toastMessage}</span>
                </div>
              )}

              {/* WhatsApp Button */}
              <a 
                href="https://wa.me/qr/RFKZSZH2PZKNA1" 
                target="_blank" 
                rel="noopener noreferrer"
                className="fixed bottom-6 right-6 z-40 bg-emerald-600 hover:bg-emerald-700 text-white p-4 rounded-full shadow-2xl flex items-center gap-3 transition-transform hover:scale-105"
                title={t.whatsappBtn}
              >
                <svg className="w-6 h-6 fill-current" viewBox="0 0 24 24"><path d="M.057 24l1.687-6.163c-1.041-1.804-1.588-3.849-1.587-5.946.003-6.556 5.338-11.891 11.893-11.891 3.181.001 6.167 1.24 8.413 3.488 2.245 2.248 3.481 5.236 3.48 8.414-.003 6.557-5.338 11.892-11.893 11.892-1.99-.001-3.951-.5-5.688-1.448l-6.305 1.654zm6.597-3.807c1.676.995 3.276 1.591 5.392 1.592 5.448 0 9.886-4.434 9.889-9.885.002-5.462-4.415-9.89-9.881-9.892-5.452 0-9.887 4.434-9.889 9.884-.001 2.225.651 3.891 1.746 5.634l-.999 3.648 3.742-.981zm11.387-5.464c-.074-.124-.272-.198-.57-.347-.297-.149-1.758-.868-2.031-.967-.272-.099-.47-.149-.669.149-.198.297-.768.967-.941 1.165-.173.198-.347.223-.644.074-.297-.149-1.255-.462-2.39-1.475-.883-.788-1.48-1.761-1.653-2.059-.173-.297-.018-.458.13-.606.134-.133.297-.347.446-.521.151-.172.2-.296.3-.495.099-.198.05-.372-.025-.521-.075-.148-.669-1.611-.916-2.206-.242-.579-.487-.501-.669-.51l-.57-.01c-.198 0-.52.074-.792.372s-1.04 1.016-1.04 2.479 1.065 2.876 1.213 3.074c.149.198 2.095 3.2 5.076 4.487.709.306 1.263.489 1.694.626.712.226 1.36.194 1.872.118.571-.085 1.758-.719 2.006-1.413.248-.695.248-1.29.173-1.414z"/></svg>
                <span className="hidden md:inline font-bold text-sm">{t.whatsappBtn}</span>
              </a>

              <!-- HEADER -->
              <header className="bg-slate-900 text-white sticky top-0 z-30 border-b border-red-600 shadow-xl">
                <div className="flex items-center justify-between px-6 py-3">
                  <div className="flex items-center gap-4">
                    <button onClick={() => setSidebarOpen(!sidebarOpen)} className="p-2 hover:bg-slate-800 rounded-lg">
                      <svg className="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path strokeLinecap="round" strokeLinejoin="round" strokeWidth="2" d="M4 6h16M4 12h16M4 18h16"></path></svg>
                    </button>
                    <div className="flex items-center gap-3">
                      <div className="bg-red-600 font-black px-3 py-1 rounded-lg text-lg">ADY</div>
                      <div>
                        <h1 className="text-sm font-bold text-red-400">{t.companyName}</h1>
                        <p className="text-xs text-slate-400">{t.systemTitle}</p>
                      </div>
                    </div>
                  </div>

                  <div className="flex items-center gap-4">
                    <button onClick={() => setLang(lang === 'ar' ? 'en' : 'ar')} className="bg-red-600 hover:bg-red-700 px-3 py-1.5 rounded-lg text-xs font-bold shadow">
                      {t.langToggle}
                    </button>
                  </div>
                </div>
              </header>

              <div className="flex">
                <!-- SIDEBAR -->
                <aside className={`${sidebarOpen ? 'w-64' : 'w-20'} bg-slate-900 text-slate-300 transition-all min-h-[calc(100vh-65px)] flex flex-col border-r border-slate-800`}>
                  <div className="p-4 space-y-1 overflow-y-auto flex-1 text-sm">
                    {[
                      { id: 'dashboard', label: t.dashboard, icon: '📊' },
                      { id: 'portfolio', label: t.portfolio, icon: '📁' },
                      { id: 'planning', label: t.planning, icon: '📅' },
                      { id: 'gantt', label: t.gantt, icon: '📈' },
                      { id: 'procurement', label: t.procurement, icon: '🚢' },
                      { id: 'risks', label: t.risks, icon: '⚠️' },
                      { id: 'reports', label: t.reports, icon: '📑' }
                    ].map(item => (
                      <button
                        key={item.id}
                        onClick={() => setActiveTab(item.id)}
                        className={`w-full flex items-center gap-3 px-3 py-2.5 rounded-xl transition ${activeTab === item.id ? 'bg-red-600 text-white font-bold' : 'hover:bg-slate-800'}`}
                      >
                        <span>{item.icon}</span>
                        {sidebarOpen && <span>{item.label}</span>}
                      </button>
                    ))}
                  </div>
                </aside>

                <!-- WORKSPACE -->
                <main className="flex-1 p-6">
                  {activeTab === 'dashboard' && (
                    <div className="space-y-6">
                      <div className="bg-gradient-to-r from-slate-900 to-blue-950 text-white p-6 rounded-2xl shadow-xl border border-red-600">
                        <span className="bg-red-600 text-xs font-bold px-3 py-1 rounded-full uppercase">المشروع النشط الرئيسي</span>
                        <h2 className="text-2xl font-black mt-2">{isAr ? currentProject.nameAr : currentProject.nameEn}</h2>
                        <p className="text-xs text-slate-300 mt-1">العميل: {currentProject.client} | الكود: {currentProject.code}</p>
                      </div>

                      <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
                        <div className="bg-white p-5 rounded-2xl shadow border-t-4 border-blue-500">
                          <p className="text-xs text-slate-500 font-bold">{t.totalProjects}</p>
                          <p className="text-2xl font-extrabold mt-1">{projects.length}</p>
                        </div>
                        <div className="bg-white p-5 rounded-2xl shadow border-t-4 border-green-500">
                          <p className="text-xs text-slate-500 font-bold">{t.actualProgress}</p>
                          <p className="text-2xl font-extrabold mt-1 text-green-600">{calculatedProgress}%</p>
                        </div>
                        <div className="bg-white p-5 rounded-2xl shadow border-t-4 border-red-500">
                          <p className="text-xs text-slate-500 font-bold">{t.scheduleVariance}</p>
                          <p className="text-2xl font-extrabold mt-1 text-red-600">- 45 يوم</p>
                        </div>
                        <div className="bg-white p-5 rounded-2xl shadow border-t-4 border-amber-500">
                          <p className="text-xs text-slate-500 font-bold">{t.procurementStatus}</p>
                          <p className="text-2xl font-extrabold mt-1 text-amber-600">65% تم الشحن</p>
                        </div>
                      </div>

                      <div className="bg-amber-50 border-l-4 border-amber-500 p-5 rounded-2xl shadow-sm">
                        <h4 className="font-bold text-amber-900">⚠️ تنبيه مبررات تمديد المشروع والانحراف الزمني</h4>
                        <p className="text-sm text-amber-800 mt-2">{t.delayReason}</p>
                      </div>

                      <div className="bg-white rounded-2xl shadow p-6">
                        <h3 className="text-lg font-bold mb-4">هيكل تقسيم العمل (WBS) والإنجاز الموزون</h3>
                        <table className="w-full text-sm text-left border-collapse">
                          <thead>
                            <tr className="bg-slate-100 border-b">
                              <th className="p-3">الكود</th>
                              <th className="p-3">اسم حزمة العمل</th>
                              <th className="p-3">الوزن</th>
                              <th className="p-3">نسبة الإنجاز</th>
                              <th className="p-3">الحالة</th>
                            </tr>
                          </thead>
                          <tbody>
                            {wbsList.map(w => (
                              <tr key={w.id} className="border-b hover:bg-slate-50">
                                <td className="p-3 font-bold text-red-600">{w.code}</td>
                                <td className="p-3 font-semibold">{isAr ? w.nameAr : w.nameEn}</td>
                                <td className="p-3">{w.weight}%</td>
                                <td className="p-3">
                                  <input 
                                    type="number" 
                                    value={w.progress}
                                    onChange={(e) => {
                                      const val = parseFloat(e.target.value) || 0;
                                      setWbsList(wbsList.map(item => item.id === w.id ? { ...item, progress: val } : item));
                                    }}
                                    className="w-20 border px-2 py-1 rounded text-xs font-bold"
                                  /> %
                                </td>
                                <td className="p-3"><span className="px-2 py-1 bg-slate-100 rounded text-xs font-bold">{w.status}</span></td>
                              </tr>
                            ))}
                          </tbody>
                        </table>
                      </div>
                    </div>
                  )}

                  {activeTab === 'gantt' && (
                    <div className="bg-white p-6 rounded-2xl shadow">
                      <h2 className="text-xl font-bold mb-4">المخطط الزمني للأنشطة (Gantt Schedule)</h2>
                      <table className="w-full text-xs text-left border-collapse">
                        <thead>
                          <tr className="bg-slate-100 border-b">
                            <th className="p-3">الكود</th>
                            <th className="p-3">اسم النشاط</th>
                            <th className="p-3">المدة</th>
                            <th className="p-3">البداية</th>
                            <th className="p-3">النهاية</th>
                            <th className="p-3">نسبة الإنجاز</th>
                          </tr>
                        </thead>
                        <tbody>
                          {activities.map(act => (
                            <tr key={act.id} className="border-b">
                              <td className="p-3 font-bold text-blue-600">{act.id}</td>
                              <td className="p-3 font-semibold">{isAr ? act.nameAr : act.nameEn}</td>
                              <td className="p-3">{act.duration} يوم</td>
                              <td className="p-3">{act.start}</td>
                              <td className="p-3">{act.finish}</td>
                              <td className="p-3">
                                <div className="w-32 bg-slate-200 h-2.5 rounded-full overflow-hidden">
                                  <div className="bg-blue-600 h-full" style={{ width: `${act.progress}%` }}></div>
                                </div>
                              </td>
                            </tr>
                          ))}
                        </tbody>
                      </table>
                    </div>
                  )}

                  {activeTab === 'reports' && (
                    <div className="bg-white p-6 rounded-2xl shadow">
                      <div className="flex justify-between items-center mb-4">
                        <h2 className="text-xl font-bold">مركز التقارير التنفيذية</h2>
                        <button onClick={() => window.print()} className="bg-slate-900 text-white px-4 py-2 rounded-xl text-xs font-bold">طباعة التقرير (Print PDF)</button>
                      </div>
                      <p className="text-sm text-slate-500">جاهز للطباعة أو التصدير بصيغة PDF فوراً.</p>
                    </div>
                  )}
                </main>
              </div>

            </div>
          );
        }

        ReactDOM.createRoot(document.getElementById('root')).render(<App />);
    </script>
</body>
</html>
```# ADY
Aldiyaa Electric 
