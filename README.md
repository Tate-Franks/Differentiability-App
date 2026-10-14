<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Differentiability Explorer</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- MathJax for TeX rendering -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-mml-chtml.js"></script>
    
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Fira+Code:wght@400;500&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
        }
        .code-font {
            font-family: 'Fira Code', monospace;
        }
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #1e293b;
        }
        ::-webkit-scrollbar-thumb {
            background: #475569;
            border-radius: 4px;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col bg-slate-900 text-slate-100">

    <header class="bg-slate-800/80 backdrop-blur border-b border-slate-700 sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 py-3 sm:px-6 lg:px-8 flex flex-col sm:flex-row justify-between items-center gap-2">
            <div class="flex items-center space-x-3">
                <div class="p-2 bg-indigo-600 rounded-lg text-white">
                    <i class="fa-solid fa-chart-line text-xl"></i>
                </div>
                <div>
                    <h1 class="text-xl font-bold bg-gradient-to-r from-indigo-400 to-cyan-400 bg-clip-text text-transparent">
                        Differentiability Explorer
                    </h1>
                    <p class="text-xs text-slate-400">Interactive Calculus & Numerical Analysis Tool</p>
                </div>
            </div>
            <!-- Navigation Tabs -->
            <nav class="flex space-x-1 bg-slate-900/60 p-1 rounded-xl border border-slate-700/60">
                <button id="tab-lab-btn" onclick="switchTab('lab')" class="px-4 py-1.5 rounded-lg text-sm font-medium transition-colors bg-indigo-600 text-white shadow-sm">
                    <i class="fa-solid fa-flask mr-1.5"></i>Visual Lab
                </button>
                <button id="tab-theory-btn" onclick="switchTab('theory')" class="px-4 py-1.5 rounded-lg text-sm font-medium text-slate-400 hover:text-white transition-colors">
                    <i class="fa-solid fa-book-open mr-1.5"></i>Theory & Guide
                </button>
            </nav>
        </div>
    </header>

    <main class="flex-1 max-w-7xl w-full mx-auto p-4 sm:p-6 lg:p-8 space-y-6">

        <!-- TAB 1: VISUAL LAB -->
        <div id="tab-lab" class="space-y-6">
            
            <div class="bg-indigo-950/40 border border-indigo-500/30 rounded-xl p-4 flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3 text-indigo-200 text-sm">
                <div class="flex items-center space-x-3">
                    <i class="fa-solid fa-compass text-indigo-400 text-lg flex-shrink-0"></i>
                    <span><strong>Help / Guide:</strong> Move <code class="bg-indigo-900/60 px-1.5 py-0.5 rounded text-indigo-300">a</code> towards suspect points; shrink <code class="bg-indigo-900/60 px-1.5 py-0.5 rounded text-indigo-300">h</code>; compare left vs right derivative estimates.</span>
                </div>
                <button onclick="resetDefaults()" class="text-xs bg-slate-800 hover:bg-slate-700 text-slate-300 px-3 py-1.5 rounded-lg border border-slate-600 transition-colors flex-shrink-0">
                    <i class="fa-solid fa-rotate-left mr-1"></i> Reset View
                </button>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">

                <div class="lg:col-span-4 space-y-5">
                    
                    <div class="bg-slate-800/80 border border-slate-700 rounded-2xl p-5 shadow-lg space-y-4">
                        <h2 class="text-base font-semibold text-slate-200 flex items-center gap-2">
                            <i class="fa-solid fa-sliders text-indigo-400"></i> Function Selection
                        </h2>

                        <div>
                            <label class="block text-xs font-medium text-slate-400 mb-1">Select Preset or Custom</label>
                            <select id="func-select" onchange="handleFunctionChange()" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-2 text-sm text-slate-200 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                                <option value="abs">f(x) = |x| (Absolute Value)</option>
                                <option value="parabola" selected>f(x) = x² (Parabola)</option>
                                <option value="smooth">f(x) = x³ - 3x + 1 (Smooth Cubic)</option>
                                <option value="cbrt">f(x) = ∛x (Vertical Tangent at 0)</option>
                                <option value="jump">Piecewise Jump Function</option>
                                <option value="custom">Custom Expression...</option>
                            </select>
                        </div>

                        <div id="custom-input-box" class="hidden space-y-1">
                            <label class="block text-xs font-medium text-slate-400">Custom Math Formula f(x)</label>
                            <div class="flex gap-2">
                                <input type="text" id="custom-expr-input" value="sin(x) + x^2/4" placeholder="e.g. sin(x), x^2 - 3, exp(x)" class="flex-1 bg-slate-900 border border-slate-700 rounded-xl px-3 py-1.5 text-xs code-font text-indigo-300 focus:outline-none focus:ring-2 focus:ring-indigo-500">
                                <button onclick="updatePlot()" class="bg-indigo-600 hover:bg-indigo-500 px-3 py-1.5 rounded-xl text-xs font-semibold text-white transition-colors">
                                    Apply
                                </button>
                            </div>
                            <span class="text-[10px] text-slate-400 block">Supports: <code class="text-slate-300">sin</code>, <code class="text-slate-300">cos</code>, <code class="text-slate-300">tan</code>, <code class="text-slate-300">abs</code>, <code class="text-slate-300">sqrt</code>, <code class="text-slate-300">cbrt</code>, <code class="text-slate-300">exp</code>, <code class="text-slate-300">log</code>, <code class="text-slate-300">^</code></span>
                        </div>

                        <div id="func-description" class="p-3 bg-slate-900/60 rounded-xl text-xs text-slate-300 border border-slate-700/50 leading-relaxed">
                        </div>
                    </div>

                    <div class="bg-slate-800/80 border border-slate-700 rounded-2xl p-5 shadow-lg space-y-4">
                        <h2 class="text-base font-semibold text-slate-200 flex items-center gap-2">
                            <i class="fa-solid fa-gauge-high text-indigo-400"></i> Sliders & Domain
                        </h2>

                        <div class="space-y-1">
                            <div class="flex justify-between items-center text-xs">
                                <span class="font-medium text-slate-300">Target Point <code class="text-indigo-400 font-bold">a</code></span>
                                <span id="val-a" class="code-font bg-slate-900 px-2 py-0.5 rounded text-indigo-400 font-semibold border border-slate-700">0.500</span>
                            </div>
                            <input type="range" id="slider-a" min="-3" max="3" step="0.05" value="0.5" oninput="updatePlot()" class="w-full accent-indigo-500 bg-slate-700 h-2 rounded-lg cursor-pointer">
                        </div>

                        <div class="space-y-1">
                            <div class="flex justify-between items-center text-xs">
                                <span class="font-medium text-slate-300">Step Size <code class="text-amber-400 font-bold">h</code></span>
                                <span id="val-h" class="code-font bg-slate-900 px-2 py-0.5 rounded text-amber-400 font-semibold border border-slate-700">0.5000</span>
                            </div>
                            <input type="range" id="slider-h" min="0.0001" max="1.5" step="0.005" value="0.5" oninput="updatePlot()" class="w-full accent-amber-500 bg-slate-700 h-2 rounded-lg cursor-pointer">
                            <p class="text-[10px] text-slate-400 flex justify-between">
                                <span>Micro (<code class="text-slate-300">0.0001</code>)</span>
                                <span>Macro (<code class="text-slate-300">1.5000</code>)</span>
                            </p>
                        </div>

                        <div class="space-y-1 pt-2 border-t border-slate-700/60">
                            <div class="flex justify-between items-center text-xs">
                                <span class="font-medium text-slate-400">View Bounds [X<sub>min</sub>, X<sub>max</sub>]</span>
                                <span id="val-range" class="code-font text-slate-400">[-3.0, 3.0]</span>
                            </div>
                            <div class="grid grid-cols-2 gap-2">
                                <input type="number" id="num-xmin" value="-3" step="0.5" onchange="updatePlot()" class="bg-slate-900 border border-slate-700 rounded-lg px-2 py-1 text-xs code-font text-center">
                                <input type="number" id="num-xmax" value="3" step="0.5" onchange="updatePlot()" class="bg-slate-900 border border-slate-700 rounded-lg px-2 py-1 text-xs code-font text-center">
                            </div>
                        </div>

                        <div class="pt-2 border-t border-slate-700/60 space-y-2">
                            <label class="block text-xs font-medium text-slate-400">Visual Overlays</label>
                            <div class="grid grid-cols-2 gap-2 text-xs">
                                <label class="flex items-center space-x-2 cursor-pointer bg-slate-900/50 p-2 rounded-lg border border-slate-700/50 hover:border-slate-600">
                                    <input type="checkbox" id="chk-secant" checked onchange="updatePlot()" class="rounded bg-slate-800 border-slate-600 text-amber-500 focus:ring-amber-500">
                                    <span class="text-slate-300">Right Secant (a+h)</span>
                                </label>
                                <label class="flex items-center space-x-2 cursor-pointer bg-slate-900/50 p-2 rounded-lg border border-slate-700/50 hover:border-slate-600">
                                    <input type="checkbox" id="chk-tangent" checked onchange="updatePlot()" class="rounded bg-slate-800 border-slate-600 text-emerald-500 focus:ring-emerald-500">
                                    <span class="text-slate-300">Tangent Line</span>
                                </label>
                                <label class="flex items-center space-x-2 cursor-pointer bg-slate-900/50 p-2 rounded-lg border border-slate-700/50 hover:border-slate-600">
                                    <input type="checkbox" id="chk-left-secant" checked onchange="updatePlot()" class="rounded bg-slate-800 border-slate-600 text-cyan-500 focus:ring-cyan-500">
                                    <span class="text-slate-300">Left Secant (a-h)</span>
                                </label>
                                <label class="flex items-center space-x-2 cursor-pointer bg-slate-900/50 p-2 rounded-lg border border-slate-700/50 hover:border-slate-600">
                                    <input type="checkbox" id="chk-points" checked onchange="updatePlot()" class="rounded bg-slate-800 border-slate-600 text-indigo-500 focus:ring-indigo-500">
                                    <span class="text-slate-300">Point Markers</span>
                                </label>
                            </div>
                        </div>

                    </div>
                </div>

                <div class="lg:col-span-8 space-y-6">

                    <div class="bg-slate-800/80 border border-slate-700 rounded-2xl p-4 shadow-lg space-y-3 relative">
                        <div class="flex justify-between items-center">
                            <h2 class="text-base font-semibold text-slate-200 flex items-center gap-2">
                                <i class="fa-solid fa-chart-area text-indigo-400"></i> Function Graph & Secant/Tangent Overlays
                            </h2>
                            <span class="text-xs text-slate-400 bg-slate-900 px-2.5 py-1 rounded-md border border-slate-700">
                                Coordinate Plane
                            </span>
                        </div>

                        <div class="relative w-full aspect-[16/9] bg-slate-950 rounded-xl overflow-hidden border border-slate-700/80">
                            <canvas id="main-canvas" class="w-full h-full block cursor-crosshair"></canvas>
                        </div>

                        <div class="flex flex-wrap items-center justify-center gap-4 text-xs pt-1 border-t border-slate-700/50 text-slate-300">
                            <span class="flex items-center gap-1.5"><span class="w-3 h-0.5 bg-indigo-400 rounded"></span> f(x) Curve</span>
                            <span class="flex items-center gap-1.5"><span class="w-3 h-0.5 bg-emerald-400 rounded"></span> Tangent Line</span>
                            <span class="flex items-center gap-1.5"><span class="w-3 h-0.5 bg-amber-400 rounded"></span> Right Secant (a+h)</span>
                            <span class="flex items-center gap-1.5"><span class="w-3 h-0.5 bg-cyan-400 rounded"></span> Left Secant (a-h)</span>
                            <span class="flex items-center gap-1.5"><span class="w-2 h-2 rounded-full bg-indigo-500 border border-white"></span> Point (a, f(a))</span>
                        </div>
                    </div>

                    <div class="bg-slate-800/80 border border-slate-700 rounded-2xl p-5 shadow-lg space-y-4">
                        
                        <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3 border-b border-slate-700/60 pb-3">
                            <h2 class="text-base font-semibold text-slate-200 flex items-center gap-2">
                                <i class="fa-solid fa-calculator text-indigo-400"></i> Numerical Diagnostics Panel
                            </h2>
                            <div id="verdict-badge" class="px-3 py-1.5 rounded-xl text-xs font-bold uppercase tracking-wide flex items-center gap-2 shadow-inner">
                            </div>
                        </div>

                        <div class="grid grid-cols-2 md:grid-cols-4 gap-3 text-xs">
                            <div class="bg-slate-900/70 p-3 rounded-xl border border-slate-700/60 space-y-1">
                                <span class="text-slate-400 text-[11px] block">Point Evaluation<br><code class="text-indigo-300">f(a)</code></span>
                                <span id="num-fa" class="text-base font-bold code-font text-indigo-300">--</span>
                            </div>
                            <div class="bg-slate-900/70 p-3 rounded-xl border border-slate-700/60 space-y-1">
                                <span class="text-slate-400 text-[11px] block">Step Evaluation<br><code class="text-amber-300">f(a+h)</code></span>
                                <span id="num-fah" class="text-base font-bold code-font text-amber-300">--</span>
                            </div>
                            <div class="bg-slate-900/70 p-3 rounded-xl border border-slate-700/60 space-y-1">
                                <span class="text-slate-400 text-[11px] block">Left Derivative Est.<br><code class="text-cyan-400">[f(a)-f(a-h)]/h</code></span>
                                <span id="num-left-deriv" class="text-base font-bold code-font text-cyan-400">--</span>
                            </div>
                            <div class="bg-slate-900/70 p-3 rounded-xl border border-slate-700/60 space-y-1">
                                <span class="text-slate-400 text-[11px] block">Right Derivative Est.<br><code class="text-amber-400">[f(a+h)-f(a)]/h</code></span>
                                <span id="num-right-deriv" class="text-base font-bold code-font text-amber-400">--</span>
                            </div>
                        </div>

                        <div id="numerical-warning" class="hidden p-3 rounded-xl bg-amber-950/40 border border-amber-500/40 text-amber-200 text-xs flex items-start space-x-2">
                            <i class="fa-solid fa-triangle-exclamation text-amber-400 text-sm mt-0.5 flex-shrink-0"></i>
                            <span id="warning-text">Numerical warning message...</span>
                        </div>
                    </div>

                    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">

                        <div class="bg-slate-800/80 border border-slate-700 rounded-2xl p-4 shadow-lg space-y-3">
                            <div class="flex justify-between items-center">
                                <h3 class="text-xs font-semibold text-slate-300 uppercase tracking-wider flex items-center gap-1.5">
                                    <i class="fa-solid fa-microscope text-indigo-400"></i> Local Linearity Microscope
                                </h3>
                                <span class="text-[10px] text-indigo-300 bg-indigo-950/80 px-2 py-0.5 rounded border border-indigo-800">
                                    Magnification: 100x
                                </span>
                            </div>
                            <p class="text-[11px] text-slate-400">
                                At differentiable points, zooming in closely flattens the curve into a straight line. Sharp corners remain pointed regardless of zoom depth.
                            </p>
                            <div class="relative w-full h-36 bg-slate-950 rounded-xl overflow-hidden border border-slate-700">
                                <canvas id="micro-canvas" class="w-full h-full block"></canvas>
                            </div>
                        </div>

                        <div class="bg-slate-800/80 border border-slate-700 rounded-2xl p-4 shadow-lg space-y-3">
                            <div class="flex justify-between items-center">
                                <h3 class="text-xs font-semibold text-slate-300 uppercase tracking-wider flex items-center gap-1.5">
                                    <i class="fa-solid fa-table text-indigo-400"></i> Limit Sequence Analyzer (h → 0)
                                </h3>
                                <span class="text-[10px] text-slate-400">Convergence Test</span>
                            </div>
                            <div class="overflow-x-auto">
                                <table class="w-full text-left text-[11px] code-font border-collapse">
                                    <thead>
                                        <tr class="border-b border-slate-700 text-slate-400">
                                            <th class="pb-1 font-medium">Step h</th>
                                            <th class="pb-1 font-medium text-cyan-400">Left (a-h)</th>
                                            <th class="pb-1 font-medium text-amber-400">Right (a+h)</th>
                                        </tr>
                                    </thead>
                                    <tbody id="limit-seq-body" class="divide-y divide-slate-800 text-slate-300">
                                    </tbody>
                                </table>
                            </div>
                        </div>

                    </div>

                </div>
            </div>
        </div>

        <!-- TAB 2: THEORY & GUIDE -->
        <div id="tab-theory" class="hidden space-y-6">
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                
                <div class="md:col-span-2 bg-slate-800/80 border border-slate-700 rounded-2xl p-6 shadow-lg space-y-5">
                    <div class="flex items-center space-x-3 border-b border-slate-700 pb-4">
                        <div class="p-2.5 bg-indigo-600/20 text-indigo-400 rounded-xl border border-indigo-500/30">
                            <i class="fa-solid fa-book-open text-xl"></i>
                        </div>
                        <div>
                            <h2 class="text-lg font-bold text-slate-100">Theory: Continuity & Differentiability</h2>
                            <p class="text-xs text-slate-400">Conceptual Definitions, Worked Examples & Limit Expressions</p>
                        </div>
                    </div>

                    <div class="bg-slate-900/90 border-l-4 border-cyan-500 p-5 rounded-r-xl space-y-3">
                        <h3 class="text-xs font-semibold text-cyan-300 uppercase tracking-wider flex items-center gap-1">
                            <i class="fa-solid fa-quote-left text-cyan-400"></i> Core Definition of Continuity
                        </h3>
                        <p class="text-sm leading-relaxed text-slate-200 italic font-serif">
                            "Continuity is defined so that a function is continuous if the limit of a function as the inputs approach a point (A) exactly equals the actual value of the output for the input of A. We see this as the graph with a single unbroken curve ot line for all the domain meaning there are no holes jumps or vertical asymptotes."
                        </p>
                    </div>

                    <div class="bg-slate-900/90 border-l-4 border-indigo-500 p-5 rounded-r-xl space-y-3">
                        <h3 class="text-xs font-semibold text-indigo-300 uppercase tracking-wider flex items-center gap-1">
                            <i class="fa-solid fa-quote-left text-indigo-400"></i> Core Definition of Differentiability
                        </h3>
                        <p class="text-sm leading-relaxed text-slate-200 italic font-serif">
                            "Differentiability is defined so that a function is differentiable if there is an exact finite derivative at the point. The derivative is defined as the limit of f(a+h)-f(a)/h as x approaches 0 meaning the limit from the left side and the right side have to be equal meaning that the point must be locally linear meaning that if you zoom in as close as possible to the point it must appear like its tangent line. This means that a function can not be differentiable if it has sharp corners,cusps or vertical tangents. As the slope can not be contradictory or undefined."
                        </p>
                    </div>

                    <div class="p-4 bg-indigo-950/30 border border-indigo-500/30 rounded-xl text-center space-y-2">
                        <span class="text-xs text-indigo-300 font-medium">Definition of Derivative via Limit of Difference Quotient</span>
                        <div class="text-base font-bold text-indigo-200">
                            $$f'(a) = \lim_{h \to 0} \frac{f(a+h) - f(a)}{h}$$
                        </div>
                    </div>

                    <div class="space-y-3 pt-2">
                        <h3 class="text-sm font-bold text-slate-200">Examples & Explanations</h3>
                        <div class="grid grid-cols-1 sm:grid-cols-2 gap-4 text-xs">
                            
                            <div class="p-4 bg-slate-900 rounded-xl border border-slate-700/80 space-y-2">
                                <h4 class="font-bold text-emerald-400 flex items-center gap-1.5">
                                    <i class="fa-solid fa-circle-check"></i> 1. Smooth & Differentiable Everywhere
                                </h4>
                                <p class="text-slate-300 font-medium"><code class="text-indigo-300">f(x) = x²</code> (Parabola)</p>
                                <p class="text-slate-300 leading-relaxed font-sans">
                                    "The curve is continuous everywhere and smooth with no sharp turns corners or holes. This means the the derivative limit exists at every point on the curve of a normal parabola. The tangent slope will always equal 2x."
                                </p>
                            </div>

                            <div class="p-4 bg-slate-900 rounded-xl border border-slate-700/80 space-y-2">
                                <h4 class="font-bold text-rose-400 flex items-center gap-1.5">
                                    <i class="fa-solid fa-circle-xmark"></i> 2. Continuous but NOT Differentiable
                                </h4>
                                <p class="text-slate-300 font-medium"><code class="text-indigo-300">f(x) = |x|</code> at <code class="text-indigo-300">x = 0</code></p>
                                <p class="text-slate-300 leading-relaxed font-sans">
                                    "The function absolute value of x is continuous everywhere with all real numbers are able to be x values. However since the definition of a derivative is a limit of the tangent line and the limit approaches two different sides depending on if you are coming from the negative or positive side the limit does not exist meaning the function is not differential."
                                </p>
                            </div>

                        </div>
                    </div>
                </div>

                <div class="space-y-5">
                    
                    <div class="bg-slate-800/80 border border-slate-700 rounded-2xl p-5 shadow-lg space-y-3">
                        <h3 class="text-sm font-semibold text-slate-200 flex items-center gap-2">
                            <i class="fa-solid fa-list-check text-indigo-400"></i> Interactive Guide
                        </h3>
                        <ul class="text-xs text-slate-300 space-y-2.5 list-disc list-inside leading-relaxed">
                            <li>Select a preset or custom function from the dropdown.</li>
                            <li>Move slider <code class="text-indigo-400 font-bold">a</code> towards suspect points (e.g., $x=0$, $x=-1$, or $x=1$).</li>
                            <li>Shrink slider <code class="text-amber-400 font-bold">h</code> towards zero.</li>
                            <li>Observe whether left/right secant slopes converge to a single slope or diverge.</li>
                            <li>Check the <strong>Local Linearity Microscope</strong> to verify if the zoomed curve flattens into a line or stays sharp.</li>
                        </ul>
                    </div>

                    <div class="bg-slate-800/80 border border-slate-700 rounded-2xl p-5 shadow-lg space-y-3">
                        <h3 class="text-sm font-semibold text-slate-200 flex items-center gap-2">
                            <i class="fa-solid fa-graduation-cap text-indigo-400"></i> Academic Reference Guide
                        </h3>
                        <p class="text-xs text-slate-400 leading-relaxed">
                            Include this citation in your report reference list as required by the assignment guidelines:
                        </p>
                        <div class="p-3 bg-slate-900 rounded-xl border border-slate-700 text-[11px] code-font text-indigo-300 space-y-1">
                            <p><strong>APA Reference Citation:</strong></p>
                            <p class="text-slate-300">Gemini & User. (2026). <i>Differentiability Explorer: Interactive Calculus Web Application</i> [Computer software]. Interactive Web Application.</p>
                        </div>
                    </div>

                </div>

            </div>
        </div>

    </main>

    <footer class="border-t border-slate-800 bg-slate-950 py-4 mt-auto text-center text-xs text-slate-500">
        <p>Interactive Educational Tool &bull; Differentiability & Continuity Explorer</p>
    </footer>

    <script>
        const state = {
            activeTab: 'lab',
            selectedFuncKey: 'parabola',
            a: 0.5,
            h: 0.5,
            xMin: -3.0,
            xMax: 3.0,
            overlays: {
                secant: true,
                tangent: true,
                leftSecant: true,
                points: true
            }
        };

        const functions = {
            abs: {
                name: 'f(x) = |x| (Absolute Value)',
                desc: 'Continuous everywhere on ℝ. Sharp corner at x = 0 where left slope is -1 and right slope is +1.',
                eval: (x) => Math.abs(x),
                suspectPoints: [0]
            },
            parabola: {
                name: 'f(x) = x² (Parabola)',
                desc: 'Smooth parabola continuous everywhere. Differentiable everywhere with tangent slope equal to 2x.',
                eval: (x) => x * x,
                suspectPoints: [0.5]
            },
            smooth: {
                name: 'f(x) = x³ - 3x + 1 (Smooth Cubic)',
                desc: 'Smooth polynomial curve. Differentiable everywhere on (-∞, +∞).',
                eval: (x) => Math.pow(x, 3) - 3 * x + 1,
                suspectPoints: [0]
            },
            cbrt: {
                name: 'f(x) = ∛x (Vertical Tangent at 0)',
                desc: 'Continuous everywhere, but has a vertical tangent line at x = 0 where denominator approaches zero.',
                eval: (x) => Math.cbrt(x),
                suspectPoints: [0]
            },
            jump: {
                name: 'Piecewise Jump Function',
                desc: 'y = -1 (x ≤ -1), y = 2x + 1 (-1 < x < 1), y = 5 (x ≥ 1). Discontinuous jumps at x = -1 and x = 1.',
                isPiecewise: true,
                eval: (x) => {
                    if (x <= -1) return -1;
                    if (x < 1) return 2 * x + 1;
                    return 5;
                },
                suspectPoints: [-1, 1]
            },
            custom: {
                name: 'Custom Mathematical Expression',
                desc: 'Custom user math expression. Parses operators and standard trigonometric/algebraic functions.',
                eval: (x) => evaluateCustomExpr(x),
                suspectPoints: [0]
            }
        };

        function evaluateCustomExpr(x) {
            const rawExpr = document.getElementById('custom-expr-input')?.value || "x^2";
            try {
                let clean = rawExpr
                    .replace(/abs/g, 'Math.abs')
                    .replace(/sin/g, 'Math.sin')
                    .replace(/cos/g, 'Math.cos')
                    .replace(/tan/g, 'Math.tan')
                    .replace(/sqrt/g, 'Math.sqrt')
                    .replace(/cbrt/g, 'Math.cbrt')
                    .replace(/exp/g, 'Math.exp')
                    .replace(/log/g, 'Math.log')
                    .replace(/\^/g, '**')
                    .replace(/\bx\b/g, `(${x})`);
                
                return Function(`"use strict"; return (${clean})`)();
            } catch (e) {
                return NaN;
            }
        }

        function switchTab(tabName) {
            state.activeTab = tabName;
            const labTab = document.getElementById('tab-lab');
            const theoryTab = document.getElementById('tab-theory');
            const labBtn = document.getElementById('tab-lab-btn');
            const theoryBtn = document.getElementById('tab-theory-btn');

            if (tabName === 'lab') {
                labTab.classList.remove('hidden');
                theoryTab.classList.add('hidden');
                labBtn.className = "px-4 py-1.5 rounded-lg text-sm font-medium transition-colors bg-indigo-600 text-white shadow-sm";
                theoryBtn.className = "px-4 py-1.5 rounded-lg text-sm font-medium text-slate-400 hover:text-white transition-colors";
                setTimeout(updatePlot, 50);
            } else {
                labTab.classList.add('hidden');
                theoryTab.classList.remove('hidden');
                theoryBtn.className = "px-4 py-1.5 rounded-lg text-sm font-medium transition-colors bg-indigo-600 text-white shadow-sm";
                labBtn.className = "px-4 py-1.5 rounded-lg text-sm font-medium text-slate-400 hover:text-white transition-colors";
                if (window.MathJax) {
                    MathJax.typesetPromise();
                }
            }
        }

        function handleFunctionChange() {
            const select = document.getElementById('func-select');
            state.selectedFuncKey = select.value;

            const customBox = document.getElementById('custom-input-box');
            if (state.selectedFuncKey === 'custom') {
                customBox.classList.remove('hidden');
            } else {
                customBox.classList.add('hidden');
            }

            const funcObj = functions[state.selectedFuncKey];
            document.getElementById('func-description').innerText = funcObj.desc;

            if (funcObj.suspectPoints && funcObj.suspectPoints.length > 0) {
                state.a = funcObj.suspectPoints[0];
                document.getElementById('slider-a').value = state.a;
            }

            updatePlot();
        }

        function resetDefaults() {
            state.a = 0.5;
            state.h = 0.5;
            state.xMin = -3.0;
            state.xMax = 3.0;

            document.getElementById('slider-a').value = 0.5;
            document.getElementById('slider-h').value = 0.5;
            document.getElementById('num-xmin').value = -3.0;
            document.getElementById('num-xmax').value = 3.0;
            document.getElementById('func-select').value = 'parabola';

            handleFunctionChange();
        }

        function evaluateFunc(x) {
            return functions[state.selectedFuncKey].eval(x);
        }

        function computeDiagnostics() {
            const a = state.a;
            const h = state.h;
            const fa = evaluateFunc(a);
            const fa_plus_h = evaluateFunc(a + h);
            const fa_minus_h = evaluateFunc(a - h);

            const rightDeriv = (fa_plus_h - fa) / h;
            const leftDeriv = (fa - fa_minus_h) / h;
            const centralDeriv = (fa_plus_h - fa_minus_h) / (2 * h);
            const diffGap = Math.abs(rightDeriv - leftDeriv);

            const eps = 1e-4;
            const fa_left_limit = evaluateFunc(a - eps);
            const fa_right_limit = evaluateFunc(a + eps);
            const isContinuous = Math.abs(fa_left_limit - fa_right_limit) < 0.08 && Math.abs(fa - fa_left_limit) < 0.08;

            let verdict = "differentiable";
            let reason = "Left and right derivative estimates match as h → 0.";

            if (!isContinuous) {
                verdict = "discontinuous";
                reason = "Not differentiable due to discontinuity/jump at x = a!";
            } else if (Math.abs(rightDeriv) > 80 || Math.abs(leftDeriv) > 80) {
                verdict = "vertical_tangent";
                reason = "Not differentiable due to vertical tangent (infinite slope)!";
            } else if (diffGap > 0.25 && h < 0.2) {
                verdict = "corner";
                reason = "Not differentiable due to sharp corner (Left slope ≠ Right slope)!";
            }

            return {
                fa, fa_plus_h, fa_minus_h,
                leftDeriv, rightDeriv, centralDeriv,
                diffGap, isContinuous, verdict, reason
            };
        }

        function renderMainCanvas() {
            const canvas = document.getElementById('main-canvas');
            if (!canvas) return;

            const rect = canvas.getBoundingClientRect();
            const dpr = window.devicePixelRatio || 1;
            canvas.width = rect.width * dpr;
            canvas.height = rect.height * dpr;

            const ctx = canvas.getContext('2d');
            ctx.scale(dpr, dpr);

            const width = rect.width;
            const height = rect.height;

            ctx.fillStyle = '#020617';
            ctx.fillRect(0, 0, width, height);

            const xMin = state.xMin;
            const xMax = state.xMax;
            
            let yMin = -2;
            let yMax = 6;

            if (state.selectedFuncKey !== 'jump') {
                yMin = Infinity;
                yMax = -Infinity;
                const samples = 200;
                const dx = (xMax - xMin) / samples;

                for (let i = 0; i <= samples; i++) {
                    const x = xMin + i * dx;
                    const y = evaluateFunc(x);
                    if (!isNaN(y) && isFinite(y)) {
                        if (y < yMin) yMin = y;
                        if (y > yMax) yMax = y;
                    }
                }

                if (yMin === Infinity || yMax === -Infinity || Math.abs(yMax - yMin) < 0.001) {
                    yMin = -3;
                    yMax = 3;
                }

                const yPad = (yMax - yMin) * 0.15 || 1;
                yMin -= yPad;
                yMax += yPad;
            }

            const toScreenX = (x) => ((x - xMin) / (xMax - xMin)) * width;
            const toScreenY = (y) => height - ((y - yMin) / (yMax - yMin)) * height;

            ctx.lineWidth = 1;
            ctx.strokeStyle = '#1e293b';
            ctx.fillStyle = '#64748b';
            ctx.font = '10px Fira Code';

            const xStep = Math.pow(10, Math.floor(Math.log10(xMax - xMin))) / 2 || 1;
            for (let x = Math.ceil(xMin / xStep) * xStep; x <= xMax; x += xStep) {
                const sx = toScreenX(x);
                ctx.beginPath();
                ctx.moveTo(sx, 0);
                ctx.lineTo(sx, height);
                ctx.stroke();
                ctx.fillText(x.toFixed(1), sx + 4, height - 6);
            }

            const originX = toScreenX(0);
            const originY = toScreenY(0);

            if (originY >= 0 && originY <= height) {
                ctx.strokeStyle = '#475569';
                ctx.lineWidth = 1.5;
                ctx.beginPath();
                ctx.moveTo(0, originY);
                ctx.lineTo(width, originY);
                ctx.stroke();
            }

            if (originX >= 0 && originX <= width) {
                ctx.strokeStyle = '#475569';
                ctx.lineWidth = 1.5;
                ctx.beginPath();
                ctx.moveTo(originX, 0);
                ctx.lineTo(originX, height);
                ctx.stroke();
            }

            ctx.lineWidth = 2.5;
            ctx.strokeStyle = '#818cf8';

            if (state.selectedFuncKey === 'jump') {
                if (xMin < -1) {
                    ctx.beginPath();
                    ctx.moveTo(toScreenX(xMin), toScreenY(-1));
                    ctx.lineTo(toScreenX(Math.min(-1, xMax)), toScreenY(-1));
                    ctx.stroke();

                    if (xMax >= -1) {
                        ctx.fillStyle = '#818cf8';
                        ctx.beginPath();
                        ctx.arc(toScreenX(-1), toScreenY(-1), 4, 0, Math.PI * 2);
                        ctx.fill();
                    }
                }

                if (xMax > -1 && xMin < 1) {
                    const startX = Math.max(-1, xMin);
                    const endX = Math.min(1, xMax);

                    ctx.beginPath();
                    ctx.moveTo(toScreenX(startX), toScreenY(2 * startX + 1));
                    ctx.lineTo(toScreenX(endX), toScreenY(2 * endX + 1));
                    ctx.stroke();

                    if (startX === -1) {
                        ctx.fillStyle = '#020617';
                        ctx.strokeStyle = '#818cf8';
                        ctx.lineWidth = 2;
                        ctx.beginPath();
                        ctx.arc(toScreenX(-1), toScreenY(-1), 4, 0, Math.PI * 2);
                        ctx.fill();
                        ctx.stroke();
                    }

                    if (endX === 1) {
                        ctx.fillStyle = '#020617';
                        ctx.strokeStyle = '#818cf8';
                        ctx.lineWidth = 2;
                        ctx.beginPath();
                        ctx.arc(toScreenX(1), toScreenY(3), 4, 0, Math.PI * 2);
                        ctx.fill();
                        ctx.stroke();
                    }
                }

                if (xMax >= 1) {
                    const startX = Math.max(1, xMin);
                    ctx.beginPath();
                    ctx.moveTo(toScreenX(startX), toScreenY(5));
                    ctx.lineTo(toScreenX(xMax), toScreenY(5));
                    ctx.stroke();

                    ctx.fillStyle = '#818cf8';
                    ctx.beginPath();
                    ctx.arc(toScreenX(1), toScreenY(5), 4, 0, Math.PI * 2);
                    ctx.fill();
                }

            } else {
                const samples = 300;
                const dx = (xMax - xMin) / samples;
                ctx.beginPath();
                let isDrawing = false;

                for (let i = 0; i <= samples; i++) {
                    const x = xMin + i * dx;
                    const y = evaluateFunc(x);

                    if (isNaN(y) || !isFinite(y)) {
                        isDrawing = false;
                        continue;
                    }

                    const sx = toScreenX(x);
                    const sy = toScreenY(y);

                    if (!isDrawing) {
                        ctx.moveTo(sx, sy);
                        isDrawing = true;
                    } else {
                        ctx.lineTo(sx, sy);
                    }
                }
                ctx.stroke();
            }

            const a = state.a;
            const h = state.h;
            const fa = evaluateFunc(a);
            const fa_plus_h = evaluateFunc(a + h);
            const fa_minus_h = evaluateFunc(a - h);

            const diag = computeDiagnostics();

            if (state.overlays.leftSecant && !isNaN(fa_minus_h)) {
                ctx.lineWidth = 1.5;
                ctx.strokeStyle = '#22d3ee';
                ctx.setLineDash([4, 4]);

                const slopeL = diag.leftDeriv;
                const xStart = xMin;
                const yStart = fa + slopeL * (xStart - a);
                const xEnd = xMax;
                const yEnd = fa + slopeL * (xEnd - a);

                ctx.beginPath();
                ctx.moveTo(toScreenX(xStart), toScreenY(yStart));
                ctx.lineTo(toScreenX(xEnd), toScreenY(yEnd));
                ctx.stroke();
                ctx.setLineDash([]);
            }

            if (state.overlays.secant && !isNaN(fa_plus_h)) {
                ctx.lineWidth = 2;
                ctx.strokeStyle = '#fbbf24';

                const slopeR = diag.rightDeriv;
                const xStart = xMin;
                const yStart = fa + slopeR * (xStart - a);
                const xEnd = xMax;
                const yEnd = fa + slopeR * (xEnd - a);

                ctx.beginPath();
                ctx.moveTo(toScreenX(xStart), toScreenY(yStart));
                ctx.lineTo(toScreenX(xEnd), toScreenY(yEnd));
                ctx.stroke();
            }

            if (state.overlays.tangent) {
                ctx.lineWidth = 2;
                ctx.strokeStyle = '#34d399';

                const slopeT = diag.centralDeriv;
                if (isFinite(slopeT)) {
                    const xStart = xMin;
                    const yStart = fa + slopeT * (xStart - a);
                    const xEnd = xMax;
                    const yEnd = fa + slopeT * (xEnd - a);

                    ctx.beginPath();
                    ctx.moveTo(toScreenX(xStart), toScreenY(yStart));
                    ctx.lineTo(toScreenX(xEnd), toScreenY(yEnd));
                    ctx.stroke();
                }
            }

            if (state.overlays.points) {
                if (isFinite(fa)) {
                    const saX = toScreenX(a);
                    const saY = toScreenY(fa);

                    ctx.fillStyle = '#6366f1';
                    ctx.strokeStyle = '#ffffff';
                    ctx.lineWidth = 2;
                    ctx.beginPath();
                    ctx.arc(saX, saY, 6, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.stroke();
                }

                if (isFinite(fa_plus_h)) {
                    const sahX = toScreenX(a + h);
                    const sahY = toScreenY(fa_plus_h);

                    ctx.fillStyle = '#f59e0b';
                    ctx.strokeStyle = '#ffffff';
                    ctx.lineWidth = 2;
                    ctx.beginPath();
                    ctx.arc(sahX, sahY, 5, 0, Math.PI * 2);
                    ctx.fill();
                    ctx.stroke();
                }
            }
        }

        function renderMicroscopeCanvas() {
            const canvas = document.getElementById('micro-canvas');
            if (!canvas) return;

            const rect = canvas.getBoundingClientRect();
            const dpr = window.devicePixelRatio || 1;
            canvas.width = rect.width * dpr;
            canvas.height = rect.height * dpr;

            const ctx = canvas.getContext('2d');
            ctx.scale(dpr, dpr);

            const width = rect.width;
            const height = rect.height;

            ctx.fillStyle = '#020617';
            ctx.fillRect(0, 0, width, height);

            const zoomDelta = 0.05;
            const xMinZoom = state.a - zoomDelta;
            const xMaxZoom = state.a + zoomDelta;

            const fa = evaluateFunc(state.a);
            let yMinZoom = fa - zoomDelta;
            let yMaxZoom = fa + zoomDelta;

            const toScreenX = (x) => ((x - xMinZoom) / (xMaxZoom - xMinZoom)) * width;
            const toScreenY = (y) => height - ((y - yMinZoom) / (yMaxZoom - yMinZoom)) * height;

            ctx.strokeStyle = '#334155';
            ctx.lineWidth = 1;
            ctx.beginPath();
            ctx.moveTo(width / 2, 0);
            ctx.lineTo(width / 2, height);
            ctx.moveTo(0, height / 2);
            ctx.lineTo(width, height / 2);
            ctx.stroke();

            ctx.lineWidth = 2.5;
            ctx.strokeStyle = '#818cf8';
            ctx.beginPath();

            const samples = 100;
            const dx = (xMaxZoom - xMinZoom) / samples;
            let started = false;

            for (let i = 0; i <= samples; i++) {
                const x = xMinZoom + i * dx;
                const y = evaluateFunc(x);
                if (isNaN(y) || !isFinite(y)) {
                    started = false;
                    continue;
                }
                const sx = toScreenX(x);
                const sy = toScreenY(y);

                if (!started) {
                    ctx.moveTo(sx, sy);
                    started = true;
                } else {
                    ctx.lineTo(sx, sy);
                }
            }
            ctx.stroke();

            ctx.fillStyle = '#6366f1';
            ctx.beginPath();
            ctx.arc(width / 2, height / 2, 4, 0, Math.PI * 2);
            ctx.fill();
        }

        function updateLimitSequenceTable() {
            const body = document.getElementById('limit-seq-body');
            if (!body) return;

            const a = state.a;
            const fa = evaluateFunc(a);
            const steps = [0.1, 0.01, 0.001, 0.0001, 0.00001];

            let html = "";
            steps.forEach((step) => {
                const leftSlope = (fa - evaluateFunc(a - step)) / step;
                const rightSlope = (evaluateFunc(a + step) - fa) / step;

                const leftStr = isFinite(leftSlope) ? leftSlope.toFixed(4) : "∞";
                const rightStr = isFinite(rightSlope) ? rightSlope.toFixed(4) : "∞";

                html += `
                    <tr>
                        <td class="py-1 text-slate-400">${step}</td>
                        <td class="py-1 text-cyan-300">${leftStr}</td>
                        <td class="py-1 text-amber-300">${rightStr}</td>
                    </tr>
                `;
            });

            body.innerHTML = html;
        }

        function updateNumericalDiagnostics() {
            const diag = computeDiagnostics();

            document.getElementById('num-fa').innerText = isFinite(diag.fa) ? diag.fa.toFixed(4) : "NaN";
            document.getElementById('num-fah').innerText = isFinite(diag.fa_plus_h) ? diag.fa_plus_h.toFixed(4) : "NaN";
            document.getElementById('num-left-deriv').innerText = isFinite(diag.leftDeriv) ? diag.leftDeriv.toFixed(4) : "∞";
            document.getElementById('num-right-deriv').innerText = isFinite(diag.rightDeriv) ? diag.rightDeriv.toFixed(4) : "∞";

            const badge = document.getElementById('verdict-badge');
            if (diag.verdict === 'differentiable') {
                badge.className = "px-3 py-1.5 rounded-xl text-xs font-bold uppercase tracking-wide flex items-center gap-2 bg-emerald-500/20 text-emerald-400 border border-emerald-500/40 shadow-inner";
                badge.innerHTML = `<i class="fa-solid fa-circle-check text-emerald-400 text-sm"></i> Likely Differentiable at a=${state.a.toFixed(2)}`;
            } else if (diag.verdict === 'vertical_tangent') {
                badge.className = "px-3 py-1.5 rounded-xl text-xs font-bold uppercase tracking-wide flex items-center gap-2 bg-purple-500/20 text-purple-400 border border-purple-500/40 shadow-inner";
                badge.innerHTML = `<i class="fa-solid fa-arrows-up-down text-purple-400 text-sm"></i> Not Differentiable (Vertical Tangent)`;
            } else if (diag.verdict === 'discontinuous') {
                badge.className = "px-3 py-1.5 rounded-xl text-xs font-bold uppercase tracking-wide flex items-center gap-2 bg-rose-500/20 text-rose-400 border border-rose-500/40 shadow-inner";
                badge.innerHTML = `<i class="fa-solid fa-scissors text-rose-400 text-sm"></i> Not Differentiable (Discontinuity / Jump)`;
            } else {
                badge.className = "px-3 py-1.5 rounded-xl text-xs font-bold uppercase tracking-wide flex items-center gap-2 bg-amber-500/20 text-amber-400 border border-amber-500/40 shadow-inner";
                badge.innerHTML = `<i class="fa-solid fa-triangle-exclamation text-amber-400 text-sm"></i> Not Differentiable (Corner / Sharp Point)`;
            }

            const warnBox = document.getElementById('numerical-warning');
            const warnText = document.getElementById('warning-text');

            if (state.h < 0.0005) {
                warnBox.classList.remove('hidden');
                warnText.innerHTML = `<strong>Floating Point Cancellation Warning:</strong> Step size <code class="bg-amber-900/60 px-1 rounded">h = ${state.h}</code> is extremely small. Subtracting near-identical floating-point numbers causes loss of precision (catastrophic cancellation).`;
            } else {
                warnBox.classList.add('hidden');
            }
        }

        function updatePlot() {
            state.a = parseFloat(document.getElementById('slider-a').value);
            state.h = parseFloat(document.getElementById('slider-h').value);
            state.xMin = parseFloat(document.getElementById('num-xmin').value);
            state.xMax = parseFloat(document.getElementById('num-xmax').value);

            state.overlays.secant = document.getElementById('chk-secant').checked;
            state.overlays.tangent = document.getElementById('chk-tangent').checked;
            state.overlays.leftSecant = document.getElementById('chk-left-secant').checked;
            state.overlays.points = document.getElementById('chk-points').checked;

            document.getElementById('val-a').innerText = state.a.toFixed(3);
            document.getElementById('val-h').innerText = state.h.toFixed(4);
            document.getElementById('val-range').innerText = `[${state.xMin.toFixed(1)}, ${state.xMax.toFixed(1)}]`;

            renderMainCanvas();
            renderMicroscopeCanvas();
            updateLimitSequenceTable();
            updateNumericalDiagnostics();
        }

        window.addEventListener('resize', () => {
            if (state.activeTab === 'lab') {
                updatePlot();
            }
        });

        window.onload = function() {
            handleFunctionChange();
            updatePlot();
        };
    </script>
</body>
</html>
