import React, { useState, useMemo, useRef, useEffect } from 'react';
import { 
  Activity, 
  TrendingUp,
  Target,
  Search,
  Languages,
  Layout,
  BookOpen,
  ArrowRightLeft,
  Split,
  Plus,
  Minus,
  Maximize,
  Hand,
  MousePointer2,
  Globe
} from 'lucide-react';

/**
 * ParabolaGraph Component with Zoom and Pan
 */
const ParabolaGraph = ({ table, mathFunc, vertexX }) => {
  const [zoom, setZoom] = useState(1);
  const [pan, setPan] = useState({ x: 0, y: 0 });
  const [isPanMode, setIsPanMode] = useState(false);
  const [isDragging, setIsDragging] = useState(false);
  const dragStart = useRef({ x: 0, y: 0 });
  
  const width = 400;
  const height = 400;
  const padding = 50;
  
  // Base ranges that get scaled by zoom and offset by pan
  const baseMinX = -8, baseMaxX = 8;
  const baseMinY = -10, baseMaxY = 12;
  
  // Calculate current visible bounds based on zoom and pan
  const minX = (baseMinX / zoom) - (pan.x / (width / (baseMaxX - baseMinX) * zoom));
  const maxX = (baseMaxX / zoom) - (pan.x / (width / (baseMaxX - baseMinX) * zoom));
  const minY = (baseMinY / zoom) - (pan.y / (height / (baseMaxY - baseMinY) * zoom));
  const maxY = (baseMaxY / zoom) - (pan.y / (height / (baseMaxY - baseMinY) * zoom));
  
  const scaleX = (x) => padding + ((x - minX) / (maxX - minX)) * (width - 2 * padding);
  const scaleY = (y) => height - (padding + ((y - minY) / (maxY - minY)) * (height - 2 * padding));

  const handleZoomIn = () => setZoom(prev => Math.min(prev + 0.25, 4));
  const handleZoomOut = () => setZoom(prev => Math.max(prev - 0.25, 0.25));
  
  const handleReset = () => {
    setZoom(1);
    setPan({ x: 0, y: 0 });
  };

  // Panning Event Handlers
  const onMouseDown = (e) => {
    if (!isPanMode) return;
    setIsDragging(true);
    dragStart.current = { x: e.clientX - pan.x, y: e.clientY - pan.y };
  };

  const onMouseMove = (e) => {
    if (!isDragging) return;
    setPan({
      x: e.clientX - dragStart.current.x,
      y: e.clientY - dragStart.current.y
    });
  };

  const onMouseUp = () => setIsDragging(false);

  const curvePoints = useMemo(() => {
    const points = [];
    const step = (maxX - minX) / 150;
    for (let x = minX - 2; x <= maxX + 2; x += step) {
      const y = mathFunc(x);
      points.push(`${scaleX(x)},${scaleY(y)}`);
    }
    return points.join(" ");
  }, [mathFunc, minX, maxX, zoom, pan]);

  const getGridTicks = (min, max) => {
    const ticks = [];
    const interval = zoom > 2 ? 0.5 : zoom < 0.5 ? 5 : 1;
    const start = Math.floor(min / interval) * interval;
    const end = Math.ceil(max / interval) * interval;
    for (let i = start; i <= end; i += interval) ticks.push(i);
    return ticks;
  };

  const xTicks = getGridTicks(minX, maxX);
  const yTicks = getGridTicks(minY, maxY);

  return (
    <div className="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm h-full flex flex-col relative group select-none">
      <div className="flex justify-between items-center mb-4">
        <h3 className="text-[10px] font-bold text-slate-400 uppercase tracking-widest flex items-center gap-2">
          <TrendingUp size={14} /> Viewport Controls
        </h3>
        
        <div className="flex items-center gap-2">
          <div className="flex bg-slate-100 p-1 rounded-xl border border-slate-200 shadow-inner">
            <button 
              onClick={() => setIsPanMode(false)}
              className={`p-1.5 rounded-lg transition-all ${!isPanMode ? 'bg-white text-indigo-600 shadow-sm' : 'text-slate-400 hover:text-slate-600'}`}
              title="Select Mode"
            >
              <MousePointer2 size={14} />
            </button>
            <button 
              onClick={() => setIsPanMode(true)}
              className={`p-1.5 rounded-lg transition-all ${isPanMode ? 'bg-white text-indigo-600 shadow-sm' : 'text-slate-400 hover:text-slate-600'}`}
              title="Pan Mode"
            >
              <Hand size={14} />
            </button>
          </div>

          <div className="flex items-center gap-1 bg-slate-50 p-1 rounded-xl border border-slate-100 shadow-sm transition-opacity group-hover:opacity-100">
            <button onClick={handleZoomOut} className="p-1.5 hover:bg-white hover:text-indigo-600 rounded-lg text-slate-400 transition-all" title="Zoom Out">
              <Minus size={14} />
            </button>
            <span className="text-[10px] font-mono font-bold px-2 text-slate-600 w-12 text-center">
              {Math.round(zoom * 100)}%
            </span>
            <button onClick={handleZoomIn} className="p-1.5 hover:bg-white hover:text-indigo-600 rounded-lg text-slate-400 transition-all" title="Zoom In">
              <Plus size={14} />
            </button>
            <button onClick={handleReset} className="ml-1 p-1.5 hover:bg-white hover:text-indigo-600 rounded-lg text-slate-400 transition-all border-l border-slate-200 pl-2" title="Reset View">
              <Maximize size={14} />
            </button>
          </div>
        </div>
      </div>

      <div 
        className={`flex-grow flex items-center justify-center relative bg-slate-50/30 rounded-2xl overflow-hidden ${isPanMode ? (isDragging ? 'cursor-grabbing' : 'cursor-grab') : 'cursor-default'}`}
        onMouseDown={onMouseDown}
        onMouseMove={onMouseMove}
        onMouseUp={onMouseUp}
        onMouseLeave={onMouseUp}
      >
        <svg viewBox={`0 0 ${width} ${height}`} className="w-full max-w-[420px] h-auto overflow-visible transition-transform duration-75">
          {xTicks.map((x) => (
            <line key={`gx-${x}`} x1={scaleX(x)} y1={0} x2={scaleX(x)} y2={height} stroke="#f1f5f9" strokeWidth="1" />
          ))}
          {yTicks.map((y) => (
            <line key={`gy-${y}`} x1={0} y1={scaleY(y)} x2={width} y2={scaleY(y)} stroke="#f1f5f9" strokeWidth="1" />
          ))}
          
          <line x1={scaleX(0)} y1={-height} x2={scaleX(0)} y2={height * 2} stroke="#cbd5e1" strokeWidth="2" />
          <line x1={-width} y1={scaleY(0)} x2={width * 2} y2={scaleY(0)} stroke="#cbd5e1" strokeWidth="2" />
          
          <line 
            x1={scaleX(vertexX)} 
            y1={-height} 
            x2={scaleX(vertexX)} 
            y2={height * 2} 
            stroke="#4f46e5" 
            strokeWidth="2" 
            strokeDasharray="6,4" 
            className="opacity-40"
          />

          {xTicks.map(x => (
            <g key={`xtick-${x}`}>
              <line x1={scaleX(x)} y1={scaleY(0)-3} x2={scaleX(x)} y2={scaleY(0)+3} stroke="#94a3b8" strokeWidth="1" />
              {x !== 0 && (
                <text x={scaleX(x)} y={scaleY(0)+14} textAnchor="middle" className="fill-slate-400 text-[8px] font-mono font-bold">
                  {x}
                </text>
              )}
            </g>
          ))}

          {yTicks.map(y => (
            <g key={`ytick-${y}`}>
              <line x1={scaleX(0)-3} y1={scaleY(y)} x2={scaleX(0)+3} y2={scaleY(y)} stroke="#94a3b8" strokeWidth="1" />
              {y !== 0 && (
                <text x={scaleX(0)-8} y={scaleY(y)+3} textAnchor="end" className="fill-slate-400 text-[8px] font-mono font-bold">
                  {y}
                </text>
              )}
            </g>
          ))}

          <polyline fill="none" stroke="#6366f1" strokeWidth="3" points={curvePoints} strokeLinecap="round" strokeLinejoin="round" className="drop-shadow-sm" />
          
          {table.map((pt, i) => {
            const cx = scaleX(pt.x);
            const cy = scaleY(pt.y);
            return (
              <circle 
                key={i} 
                cx={cx} 
                cy={cy} 
                r="4.5" 
                className={`${pt.y === 0 ? "fill-emerald-500" : pt.x === 0 ? "fill-sky-500" : "fill-indigo-600"} stroke-white stroke-2 transition-all duration-300`} 
              />
            );
          })}
          
          <text 
            x={scaleX(vertexX) + 8} 
            y={scaleY(maxY - 0.5)} 
            className="fill-indigo-700 text-[10px] font-black"
          >
            AOS: x = {vertexX}
          </text>
        </svg>
      </div>

      {isPanMode && !isDragging && (
        <div className="absolute bottom-10 left-1/2 -translate-x-1/2 bg-slate-900/80 text-white text-[9px] px-3 py-1.5 rounded-full backdrop-blur-sm pointer-events-none animate-bounce">
          Click and drag to pan the view
        </div>
      )}
    </div>
  );
};

/**
 * ExplanationTab Component with Bilingual Support
 */
const ExplanationTab = () => {
  const [lang, setLang] = useState('en'); // 'en', 'es', 'ht'

  const translations = {
    en: {
      title: "Reading the Table",
      rootsTitle: "Roots (X-Intercepts)",
      rootsDesc: "Look at the Y column. Anywhere you see a 0, that row is an x-intercept.",
      yIntTitle: "Y-Intercept",
      yIntDesc: "Look at the X column. When x = 0, the matching y-value is your intercept.",
      vertexTitle: "The Vertex",
      vertexDesc: "Find the turning point. Look for symmetry in the Y column. It's the point where values stop decreasing and start increasing.",
      aosTitle: "Axis of Symmetry",
      aosDesc: "A vertical line that cuts the parabola in half. It always has the equation x = h (vertex x-coordinate).",
      langLabel: "English"
    },
    es: {
      title: "Cómo Leer la Tabla",
      rootsTitle: "Raíces (Intersecciones en X)",
      rootsDesc: "Mira la columna Y. En cualquier lugar donde veas un 0, esa fila es una intersección con el eje x.",
      yIntTitle: "Intersección en Y",
      yIntDesc: "Mira la columna X. Cuando x = 0, el valor de y correspondiente es tu intersección.",
      vertexTitle: "El Vértice",
      vertexDesc: "Encuentra el punto de giro. Busca simetría en la columna Y. Es el punto donde los valores dejan de disminuir y comienzan a aumentar.",
      aosTitle: "Eje de Simetría",
      aosDesc: "Una línea vertical que divide la parábola por la mitad. Siempre tiene la ecuación x = h (coordenada x del vértice).",
      langLabel: "Español"
    },
    ht: {
      title: "Kijan pou li Tablo a",
      rootsTitle: "Rasin (Entèsèp X)",
      rootsDesc: "Gade kolòn Y la. Nenpòt kote ou wè yon 0, ranje sa a se yon entèsèp x.",
      yIntTitle: "Entèsèp Y",
      yIntDesc: "Gade kolòn X la. Lè x = 0, valè y ki koresponn lan se entèsèp ou a.",
      vertexTitle: "Vèktèks la",
      vertexDesc: "Jwenn pwen kote li vire a. Chèche simetri nan kolòn Y la. Se pwen kote valè yo sispann desann epi yo kòmanse moute.",
      aosTitle: "Aks Simetri",
      aosDesc: "Yon liy vètikal ki koupe parabòl la an de. Li toujou gen ekwasyon x = h (kowòdone x vèktèks la).",
      langLabel: "Kreyòl Ayisyen"
    }
  };

  const t = translations[lang];

  return (
    <div className="max-w-4xl mx-auto space-y-8 animate-in fade-in slide-in-from-bottom-4 duration-500 pb-12">
      <div className="flex justify-center mb-4">
        <div className="flex bg-white p-1 rounded-2xl shadow-sm border border-slate-200">
          {Object.keys(translations).map((l) => (
            <button
              key={l}
              onClick={() => setLang(l)}
              className={`px-4 py-2 rounded-xl text-[11px] font-bold transition-all ${
                lang === l ? 'bg-indigo-600 text-white shadow-md' : 'text-slate-500 hover:bg-slate-50'
              }`}
            >
              {translations[l].langLabel}
            </button>
          ))}
        </div>
      </div>

      <section className="bg-white p-8 rounded-[2.5rem] shadow-sm border border-slate-200">
        <h2 className="text-2xl font-black mb-6 text-slate-900 flex items-center gap-3">
          <BookOpen className="text-indigo-600" size={28} />
          {t.title}
        </h2>
        
        <div className="grid grid-cols-1 md:grid-cols-2 gap-8">
          <div className="space-y-6">
            <div className="p-6 bg-slate-50 rounded-2xl border-l-4 border-emerald-500">
              <h3 className="font-black text-slate-800 flex items-center gap-2 mb-2">
                <Target size={18} className="text-emerald-500" /> {t.rootsTitle}
              </h3>
              <p className="text-sm text-slate-600 leading-relaxed">
                {t.rootsDesc.split('0').map((part, i, arr) => (
                  <React.Fragment key={i}>
                    {part}{i < arr.length - 1 && <span className="font-mono font-bold text-emerald-600 text-lg">0</span>}
                  </React.Fragment>
                ))}
              </p>
            </div>

            <div className="p-6 bg-slate-50 rounded-2xl border-l-4 border-sky-500">
              <h3 className="font-black text-slate-800 flex items-center gap-2 mb-2">
                <ArrowRightLeft size={18} className="text-sky-500" /> {t.yIntTitle}
              </h3>
              <p className="text-sm text-slate-600 leading-relaxed">
                {t.yIntDesc.split('x = 0').map((part, i, arr) => (
                  <React.Fragment key={i}>
                    {part}{i < arr.length - 1 && <span className="font-mono font-bold text-sky-600 text-lg">x = 0</span>}
                  </React.Fragment>
                ))}
              </p>
            </div>
          </div>

          <div className="space-y-6">
            <div className="p-6 bg-indigo-50 rounded-2xl border-l-4 border-indigo-600">
              <h3 className="font-black text-slate-800 flex items-center gap-2 mb-2">
                <Activity size={18} className="text-indigo-600" /> {t.vertexTitle}
              </h3>
              <p className="text-sm text-slate-600 leading-relaxed">
                {t.vertexDesc}
              </p>
            </div>

            <div className="p-6 bg-slate-900 text-white rounded-2xl shadow-lg border-l-4 border-indigo-400">
              <h3 className="font-black text-indigo-300 flex items-center gap-2 mb-2 italic">
                <Split size={18} /> {t.aosTitle}
              </h3>
              <p className="text-sm text-slate-300 leading-relaxed">
                {t.aosDesc.split('x = h').map((part, i, arr) => (
                  <React.Fragment key={i}>
                    {part}{i < arr.length - 1 && <span className="text-white font-mono font-bold">x = h</span>}
                  </React.Fragment>
                ))}
              </p>
            </div>
          </div>
        </div>
      </section>
    </div>
  );
};

/**
 * Main App
 */
export default function App() {
  const [activeTab, setActiveTab] = useState('lab');
  const [activeExample, setActiveExample] = useState(0);

  const examples = [
    { 
      equation: "f(x) = x²", 
      mathFunc: (x) => x * x, 
      table: [{ x: -2, y: 4 }, { x: -1, y: 1 }, { x: 0, y: 0 }, { x: 1, y: 1 }, { x: 2, y: 4 }], 
      features: { vertex: "(0, 0)", xInt: "(0, 0)", yInt: "(0, 0)", symmetry: "x = 0", vertexX: 0 }
    },
    { 
      equation: "f(x) = x² - 4", 
      mathFunc: (x) => x * x - 4, 
      table: [{ x: -3, y: 5 }, { x: -2, y: 0 }, { x: 0, y: -4 }, { x: 2, y: 0 }, { x: 3, y: 5 }], 
      features: { vertex: "(0, -4)", xInt: "(-2, 0), (2, 0)", yInt: "(0, -4)", symmetry: "x = 0", vertexX: 0 }
    },
    { 
      equation: "f(x) = -x²", 
      mathFunc: (x) => -x * x, 
      table: [{ x: -2, y: -4 }, { x: -1, y: -1 }, { x: 0, y: 0 }, { x: 1, y: -1 }, { x: 2, y: -4 }], 
      features: { vertex: "(0, 0)", xInt: "(0, 0)", yInt: "(0, 0)", symmetry: "x = 0", vertexX: 0 }
    },
    { 
      equation: "f(x) = x² + 2x + 1", 
      mathFunc: (x) => x * x + 2 * x + 1, 
      table: [{ x: -3, y: 4 }, { x: -2, y: 1 }, { x: -1, y: 0 }, { x: 0, y: 1 }, { x: 1, y: 4 }], 
      features: { vertex: "(-1, 0)", xInt: "(-1, 0)", yInt: "(0, 1)", symmetry: "x = -1", vertexX: -1 }
    },
    { 
      equation: "f(x) = x² - 2x - 3", 
      mathFunc: (x) => x * x - 2 * x - 3, 
      table: [{ x: -1, y: 0 }, { x: 0, y: -3 }, { x: 1, y: -4 }, { x: 2, y: -3 }, { x: 3, y: 0 }], 
      features: { vertex: "(1, -4)", xInt: "(-1, 0), (3, 0)", yInt: "(0, -3)", symmetry: "x = 1", vertexX: 1 }
    },
    { 
      equation: "f(x) = -x² + 4x - 3", 
      mathFunc: (x) => -x * x + 4 * x - 3, 
      table: [{ x: 0, y: -3 }, { x: 1, y: 0 }, { x: 2, y: 1 }, { x: 3, y: 0 }, { x: 4, y: -3 }], 
      features: { vertex: "(2, 1)", xInt: "(1, 0), (3, 0)", yInt: "(0, -3)", symmetry: "x = 2", vertexX: 2 }
    },
    { 
      equation: "f(x) = 2x² - 8", 
      mathFunc: (x) => 2 * x * x - 8, 
      table: [{ x: -2, y: 0 }, { x: -1, y: -6 }, { x: 0, y: -8 }, { x: 1, y: -6 }, { x: 2, y: 0 }], 
      features: { vertex: "(0, -8)", xInt: "(-2, 0), (2, 0)", yInt: "(0, -8)", symmetry: "x = 0", vertexX: 0 }
    },
    { 
      equation: "f(x) = -x² + 6x - 5", 
      mathFunc: (x) => -x * x + 6 * x - 5, 
      table: [{ x: 1, y: 0 }, { x: 2, y: 3 }, { x: 3, y: 4 }, { x: 4, y: 3 }, { x: 5, y: 0 }], 
      features: { vertex: "(3, 4)", xInt: "(1, 0), (5, 0)", yInt: "(0, -5)", symmetry: "x = 3", vertexX: 3 }
    },
    { 
      equation: "f(x) = (x-4)² - 1", 
      mathFunc: (x) => Math.pow(x - 4, 2) - 1, 
      table: [{ x: 3, y: 0 }, { x: 4, y: -1 }, { x: 5, y: 0 }, { x: 6, y: 3 }, { x: 7, y: 8 }], 
      features: { vertex: "(4, -1)", xInt: "(3, 0), (5, 0)", yInt: "(0, 15)", symmetry: "x = 4", vertexX: 4 }
    },
    { 
      equation: "f(x) = -2x² + 4", 
      mathFunc: (x) => -2 * x * x + 4, 
      table: [{ x: -2, y: -4 }, { x: -1, y: 2 }, { x: 0, y: 4 }, { x: 1, y: 2 }, { x: 2, y: -4 }], 
      features: { vertex: "(0, 4)", xInt: "≈±1.41", yInt: "(0, 4)", symmetry: "x = 0", vertexX: 0 }
    }
  ];

  const current = examples[activeExample];

  return (
    <div className="min-h-screen bg-[#fcfdfe] p-4 md:p-8 font-sans text-slate-900">
      <div className="max-w-6xl mx-auto">
        <header className="mb-10 flex flex-col lg:flex-row lg:items-center justify-between gap-6">
          <div>
            <h1 className="text-2xl font-black text-slate-900 flex items-center gap-3">
              <Activity className="text-indigo-600" size={24} />
              Quadratic Table Explorer
            </h1>
            <p className="text-slate-400 text-[10px] font-bold uppercase tracking-widest mt-1">Interactive Learning Module</p>
          </div>
          
          <nav className="flex bg-white p-1 rounded-2xl shadow-sm border border-slate-100">
            {[
              { id: 'lab', icon: <Layout size={16}/>, label: 'Explore' },
              { id: 'explanation', icon: <BookOpen size={16}/>, label: 'Explanation' },
              { id: 'translate', icon: <Globe size={16}/>, label: 'Languages' }
            ].map(tab => (
              <button
                key={tab.id}
                onClick={() => setActiveTab(tab.id)}
                className={`flex items-center gap-2 px-5 py-2 rounded-xl text-[11px] font-bold transition-all ${
                  activeTab === tab.id ? 'bg-indigo-600 text-white shadow-md' : 'text-slate-500 hover:text-slate-800'
                }`}
              >
                {tab.icon} {tab.label}
              </button>
            ))}
          </nav>
        </header>

        {activeTab === 'lab' && (
          <div className="grid grid-cols-1 lg:grid-cols-12 gap-6">
            <div className="lg:col-span-3 space-y-6">
              <div className="bg-white p-5 rounded-3xl border border-slate-100 shadow-sm">
                <h4 className="text-[10px] font-black text-slate-400 uppercase tracking-widest mb-4">Level Selection</h4>
                <div className="grid grid-cols-5 gap-2">
                  {examples.map((_, i) => (
                    <button key={i} onClick={() => setActiveExample(i)} className={`h-10 rounded-xl text-xs font-bold border-2 ${activeExample === i ? 'border-indigo-600 bg-indigo-50 text-indigo-700' : 'border-transparent bg-slate-50 text-slate-500 hover:bg-slate-100'}`}>
                      {i + 1}
                    </button>
                  ))}
                </div>
                <div className="mt-4 p-3 bg-slate-900 rounded-xl text-white font-mono text-center text-xs">{current.equation}</div>
              </div>
              
              <div className="bg-white p-5 rounded-3xl border border-slate-100 shadow-sm">
                <h4 className="text-[10px] font-black text-slate-400 uppercase tracking-widest mb-4">Numerical Data</h4>
                <div className="rounded-2xl border border-slate-200 overflow-hidden shadow-sm">
                  <table className="w-full text-xs font-mono">
                    <thead className="bg-slate-100 border-b border-slate-200">
                      <tr>
                        <th className="p-3 text-left font-black text-slate-500">X</th>
                        <th className="p-3 text-right font-black text-slate-500">Y</th>
                      </tr>
                    </thead>
                    <tbody className="divide-y divide-slate-100">
                      {current.table.map((row, i) => {
                        const isVertex = `(${row.x}, ${row.y})` === current.features.vertex;
                        const isRoot = row.y === 0;
                        const isYInt = row.x === 0;

                        return (
                          <tr 
                            key={i} 
                            className={`transition-colors duration-200 ${
                              isVertex ? "bg-indigo-100/70" : 
                              isRoot ? "bg-emerald-100/70" : 
                              isYInt ? "bg-sky-100/70" : "hover:bg-slate-50"
                            }`}
                          >
                            <td className={`p-3 font-bold ${
                                isVertex ? 'text-indigo-900' : 
                                isRoot ? 'text-emerald-900' : 
                                isYInt ? 'text-sky-900' : 'text-slate-500'
                              }`}>
                              {row.x}
                            </td>
                            <td className={`p-3 text-right font-black text-sm ${
                                isVertex ? 'text-indigo-700 underline decoration-2' : 
                                isRoot ? 'text-emerald-600' : 
                                isYInt ? 'text-sky-600' : 'text-slate-800'
                              }`}>
                              {row.y}
                            </td>
                          </tr>
                        );
                      })}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>

            <div className="lg:col-span-5">
              <ParabolaGraph table={current.table} mathFunc={current.mathFunc} vertexX={current.features.vertexX} />
            </div>

            <div className="lg:col-span-4 space-y-4">
              <div className="bg-slate-900 text-white p-8 rounded-[2.5rem] shadow-xl">
                <h4 className="text-[10px] font-black text-slate-500 uppercase tracking-widest mb-8 flex items-center gap-2">
                  <Search size={14} /> Feature Extraction
                </h4>
                <div className="space-y-6">
                  <div className="flex items-center justify-between group">
                    <div>
                      <p className="text-[10px] font-black text-indigo-400 uppercase mb-1">Vertex</p>
                      <p className="text-2xl font-black font-mono tracking-tighter text-indigo-50">{current.features.vertex}</p>
                    </div>
                    <div className="text-right">
                      <p className="text-[10px] font-black text-slate-500 uppercase mb-1">Symmetry</p>
                      <p className="text-xl font-black font-mono text-indigo-300">{current.features.symmetry}</p>
                    </div>
                  </div>
                  <div>
                    <p className="text-[10px] font-black text-emerald-400 uppercase mb-1">X-Intercepts</p>
                    <p className="text-2xl font-black font-mono tracking-tighter text-emerald-50">{current.features.xInt}</p>
                  </div>
                  <div>
                    <p className="text-[10px] font-black text-sky-400 uppercase mb-1">Y-Intercept</p>
                    <p className="text-2xl font-black font-mono tracking-tighter text-sky-50">{current.features.yInt}</p>
                  </div>
                </div>
              </div>

              <div className="bg-white p-6 rounded-3xl border border-slate-100 flex gap-3">
                <Split size={18} className="text-indigo-600 shrink-0" />
                <p className="text-xs text-slate-500 leading-relaxed font-medium">
                  Switch to the <Hand size={12} className="inline mx-0.5" /> <strong>Pan Tool</strong> to explore the graph. Reset your view anytime with the <Maximize size={12} className="inline mx-0.5" /> button.
                </p>
              </div>
            </div>
          </div>
        )}

        {activeTab === 'explanation' && <ExplanationTab />}

        {activeTab === 'translate' && (
          <div className="grid grid-cols-1 xl:grid-cols-2 gap-8 animate-in fade-in slide-in-from-bottom-4 duration-500 pb-12">
            <section className="bg-white p-8 rounded-[2.5rem] shadow-sm border border-slate-200">
               <h2 className="text-2xl font-black mb-6 text-slate-900 flex items-center gap-3">
                <Languages className="text-indigo-600" size={28} />
                Leyendo la Tabla (ES)
              </h2>
              <p className="text-slate-600">Busque el vértice, las intersecciones y la simetría en la tabla de datos. Use las herramientas de zoom y desplazamiento para navegar.</p>
            </section>
            <section className="bg-white p-8 rounded-[2.5rem] shadow-sm border border-slate-200">
               <h2 className="text-2xl font-black mb-6 text-slate-900 flex items-center gap-3">
                <Languages className="text-indigo-600" size={28} />
                Lekti Tablo a (HT)
              </h2>
              <p className="text-slate-600">Chèche vèktèks la, entèsèp yo, ak simetri nan tablo done a. Sèvi ak zouti rale ak deplase pou navige.</p>
            </section>
          </div>
        )}
      </div>
    </div>
  );
}
