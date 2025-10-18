<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Royal Malware Simulator - YASHRAJSINH RATHOD ZANZMER</title>
    <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
</head>
<body>
    <div id="root"></div>
    
    <script type="text/babel">
        const { useState, useEffect } = React;
        
        // Lucide React icons as SVG components
        const Play = ({ className }) => (
            <svg className={className} fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M5 3l14 9-14 9V3z" />
            </svg>
        );
        
        const Pause = ({ className }) => (
            <svg className={className} fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M10 9v6m4-6v6m7-3a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
        );
        
        const RotateCcw = ({ className }) => (
            <svg className={className} fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M3 10h10a8 8 0 018 8v2M3 10l6 6m-6-6l6-6" />
            </svg>
        );
        
        const Info = ({ className }) => (
            <svg className={className} fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <circle cx="12" cy="12" r="10" strokeWidth={2} />
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 16v-4m0-4h.01" />
            </svg>
        );
        
        const Shield = ({ className }) => (
            <svg className={className} fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 22s8-4 8-10V5l-8-3-8 3v7c0 6 8 10 8 10z" />
            </svg>
        );
        
        const AlertTriangle = ({ className }) => (
            <svg className={className} fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M10.29 3.86L1.82 18a2 2 0 001.71 3h16.94a2 2 0 001.71-3L13.71 3.86a2 2 0 00-3.42 0z" />
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 9v4m0 4h.01" />
            </svg>
        );
        
        const Crown = ({ className }) => (
            <svg className={className} fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 2l3.09 6.26L22 9.27l-5 4.87 1.18 6.88L12 17.77l-6.18 3.25L7 14.14 2 9.27l6.91-1.01L12 2z" />
            </svg>
        );
        
        const Monitor = ({ className }) => (
            <svg className={className} fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <rect x="2" y="3" width="20" height="14" rx="2" strokeWidth={2} />
                <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M8 21h8m-4-4v4" />
            </svg>
        );

        const MalwareSimulator = () => {
          const [files, setFiles] = useState([]);
          const [systems, setSystems] = useState([]);
          const [isRunning, setIsRunning] = useState(false);
          const [malwareType, setMalwareType] = useState('virus');
          const [speed, setSpeed] = useState(1000);
          const [logs, setLogs] = useState([]);
          const [stats, setStats] = useState({ infected: 0, total: 20 });

          useEffect(() => {
            initializeEnvironment();
          }, []);

          const initializeEnvironment = () => {
            const initialFiles = Array.from({ length: 20 }, (_, i) => ({
              id: i,
              name: file_${i}.txt,
              infected: false,
              type: 'file',
              generation: 0
            }));
            
            const initialSystems = Array.from({ length: 8 }, (_, i) => ({
              id: i,
              name: System_${i + 1},
              infected: false,
              type: 'system'
            }));

            setFiles(initialFiles);
            setSystems(initialSystems);
            setLogs([]);
            setStats({ infected: 0, total: 20 });
          };

          useEffect(() => {
            let interval;
            if (isRunning) {
              interval = setInterval(() => {
                if (malwareType === 'virus') {
                  simulateVirus();
                } else if (malwareType === 'worm') {
                  simulateWorm();
                } else if (malwareType === 'trojan') {
                  simulateTrojan();
                }
              }, speed);
            }
            return () => clearInterval(interval);
          }, [isRunning, malwareType, files, systems, speed]);

          const addLog = (message, type = 'info') => {
            const timestamp = new Date().toLocaleTimeString();
            setLogs(prev => [{
              message,
              type,
              timestamp,
              id: Date.now()
            }, ...prev].slice(0, 10));
          };

          const simulateVirus = () => {
            setFiles(prevFiles => {
              const infectedFiles = prevFiles.filter(f => f.infected);
              if (infectedFiles.length === 0) {
                const randomIndex = Math.floor(Math.random() * prevFiles.length);
                const newFiles = [...prevFiles];
                newFiles[randomIndex] = { ...newFiles[randomIndex], infected: true, generation: 1 };
                addLog(Virus infected ${newFiles[randomIndex].name}, 'danger');
                setStats(prev => ({ ...prev, infected: prev.infected + 1 }));
                return newFiles;
              }

              const uninfectedFiles = prevFiles.filter(f => !f.infected);
              if (uninfectedFiles.length === 0) return prevFiles;

              const newFiles = [...prevFiles];
              infectedFiles.forEach(infectedFile => {
                if (Math.random() > 0.7) {
                  const target = uninfectedFiles[Math.floor(Math.random() * uninfectedFiles.length)];
                  if (target) {
                    const targetIndex = newFiles.findIndex(f => f.id === target.id);
                    newFiles[targetIndex] = { 
                      ...newFiles[targetIndex], 
                      infected: true,
                      generation: infectedFile.generation + 1
                    };
                    addLog(Virus replicated from ${infectedFile.name} to ${target.name}, 'danger');
                    setStats(prev => ({ ...prev, infected: prev.infected + 1 }));
                    uninfectedFiles.splice(uninfectedFiles.indexOf(target), 1);
                  }
                }
              });
              return newFiles;
            });
          };

          const simulateWorm = () => {
            setSystems(prevSystems => {
              const infectedSystems = prevSystems.filter(s => s.infected);
              if (infectedSystems.length === 0) {
                const randomIndex = Math.floor(Math.random() * prevSystems.length);
                const newSystems = [...prevSystems];
                newSystems[randomIndex] = { ...newSystems[randomIndex], infected: true };
                addLog(Worm infiltrated ${newSystems[randomIndex].name}, 'warning');
                return newSystems;
              }

              const uninfectedSystems = prevSystems.filter(s => !s.infected);
              if (uninfectedSystems.length === 0) return prevSystems;

              if (Math.random() > 0.5) {
                const newSystems = [...prevSystems];
                const target = uninfectedSystems[Math.floor(Math.random() * uninfectedSystems.length)];
                const targetIndex = newSystems.findIndex(s => s.id === target.id);
                newSystems[targetIndex] = { ...newSystems[targetIndex], infected: true };
                addLog(Worm spread to ${target.name} via network, 'warning');
                return newSystems;
              }
              return prevSystems;
            });
          };

          const simulateTrojan = () => {
            setFiles(prevFiles => {
              const uninfectedFiles = prevFiles.filter(f => !f.infected);
              if (uninfectedFiles.length === 0) return prevFiles;

              if (Math.random() > 0.8) {
                const newFiles = [...prevFiles];
                const target = uninfectedFiles[Math.floor(Math.random() * uninfectedFiles.length)];
                const targetIndex = newFiles.findIndex(f => f.id === target.id);
                newFiles[targetIndex] = { ...newFiles[targetIndex], infected: true, disguised: true };
                addLog(Trojan disguised as ${target.name}, 'critical');
                setStats(prev => ({ ...prev, infected: prev.infected + 1 }));
                return newFiles;
              }
              return prevFiles;
            });
          };

          const toggleSimulation = () => {
            setIsRunning(!isRunning);
            if (!isRunning) {
              addLog(Started ${malwareType} simulation, 'info');
            } else {
              addLog(Paused simulation, 'info');
            }
          };

          const reset = () => {
            setIsRunning(false);
            initializeEnvironment();
            addLog('Environment reset', 'info');
          };

          return (
            <div className="min-h-screen bg-gradient-to-br from-indigo-950 via-purple-950 to-amber-950 p-6 relative overflow-hidden">
              <div className="absolute inset-0 opacity-10">
                <div className="absolute top-0 left-0 w-full h-full" style={{
                  backgroundImage: repeating-linear-gradient(45deg, transparent, transparent 35px, rgba(255,215,0,0.1) 35px, rgba(255,215,0,0.1) 70px)
                }}></div>
              </div>

              <div className="max-w-7xl mx-auto relative z-10">
                <div className="bg-gradient-to-r from-amber-600 via-yellow-500 to-amber-600 rounded-3xl p-8 mb-6 shadow-2xl border-4 border-amber-400 relative overflow-hidden">
                  <div className="absolute inset-0 bg-gradient-to-r from-transparent via-white to-transparent opacity-20 animate-pulse"></div>
                  <div className="relative z-10">
                    <div className="text-center mb-6">
                      <div className="flex items-center justify-center gap-3 mb-3">
                        <Crown className="w-10 h-10 text-amber-900" />
                        <h2 className="text-3xl font-bold text-amber-900 tracking-widest" style={{fontFamily: 'serif'}}>
                          YASHRAJSINH RATHOD ZANZMER
                        </h2>
                        <Crown className="w-10 h-10 text-amber-900" />
                      </div>
                      <div className="h-1 w-64 mx-auto bg-gradient-to-r from-transparent via-amber-900 to-transparent"></div>
                    </div>
                    <div className="flex items-center justify-between">
                      <div className="flex items-center gap-4">
                        <Shield className="w-12 h-12 text-amber-900" />
                        <div>
                          <h1 className="text-4xl font-bold text-amber-900" style={{fontFamily: 'serif'}}>Malware Behavior Simulator</h1>
                          <p className="text-amber-800 text-lg italic">Royal Cybersecurity Chamber</p>
                        </div>
                      </div>
                      <AlertTriangle className="w-10 h-10 text-red-800 animate-pulse" />
                    </div>
                  </div>
                </div>

                <div className="bg-gradient-to-br from-purple-900/80 to-indigo-900/80 backdrop-blur-lg rounded-3xl p-6 mb-6 shadow-2xl border-2 border-amber-500/50">
                  <div className="grid grid-cols-1 md:grid-cols-4 gap-4">
                    <div>
                      <label className="block text-sm font-medium text-amber-300 mb-2" style={{fontFamily: 'serif'}}>Malware Type</label>
                      <select
                        value={malwareType}
                        onChange={(e) => {
                          setMalwareType(e.target.value);
                          reset();
                        }}
                        className="w-full bg-indigo-950/80 text-amber-100 rounded-xl px-4 py-3 border-2 border-amber-500/50 focus:outline-none focus:ring-2 focus:ring-amber-400 font-medium"
                      >
                        <option value="virus">👑 Virus (Replicates)</option>
                        <option value="worm">👑 Worm (Network Spread)</option>
                        <option value="trojan">👑 Trojan (Disguised)</option>
                      </select>
                    </div>
                    
                    <div>
                      <label className="block text-sm font-medium text-amber-300 mb-2" style={{fontFamily: 'serif'}}>Speed</label>
                      <select
                        value={speed}
                        onChange={(e) => setSpeed(Number(e.target.value))}
                        className="w-full bg-indigo-950/80 text-amber-100 rounded-xl px-4 py-3 border-2 border-amber-500/50 focus:outline-none focus:ring-2 focus:ring-amber-400 font-medium"
                      >
                        <option value="2000">Slow</option>
                        <option value="1000">Normal</option>
                        <option value="500">Fast</option>
                      </select>
                    </div>

                    <div className="flex items-end gap-2">
                      <button
                        onClick={toggleSimulation}
                        className="flex-1 bg-gradient-to-r from-amber-600 to-yellow-600 hover:from-amber-700 hover:to-yellow-700 text-amber-950 px-6 py-3 rounded-xl font-bold transition-all duration-200 flex items-center justify-center gap-2 shadow-lg border-2 border-amber-400"
                      >
                        {isRunning ? <Pause className="w-5 h-5" /> : <Play className="w-5 h-5" />}
                        {isRunning ? 'Pause' : 'Start'}
                      </button>
                      <button
                        onClick={reset}
                        className="bg-gradient-to-r from-purple-700 to-indigo-700 hover:from-purple-800 hover:to-indigo-800 text-amber-100 px-6 py-3 rounded-xl font-bold transition-all duration-200 flex items-center gap-2 shadow-lg border-2 border-purple-500"
                      >
                        <RotateCcw className="w-5 h-5" />
                        Reset
                      </button>
                    </div>

                    <div className="bg-gradient-to-br from-red-900/60 to-orange-900/60 rounded-xl p-4 border-2 border-amber-500/70 shadow-lg">
                      <div className="text-amber-300 text-sm font-bold mb-1" style={{fontFamily: 'serif'}}>Infection Rate</div>
                      <div className="text-4xl font-bold text-amber-100">
                        {malwareType === 'worm' 
                          ? ${systems.filter(s => s.infected).length}/${systems.length}
                          : ${stats.infected}/${stats.total}}
                      </div>
                    </div>
                  </div>
                </div>

                <div className="grid grid-cols-1 lg:grid-cols-3 gap-6">
                  <div className="lg:col-span-2 bg-gradient-to-br from-purple-900/80 to-indigo-900/80 backdrop-blur-lg rounded-3xl p-6 shadow-2xl border-2 border-amber-500/50">
                    <h2 className="text-2xl font-bold text-amber-300 mb-4 flex items-center gap-2" style={{fontFamily: 'serif'}}>
                      <Info className="w-6 h-6 text-amber-400" />
                      {malwareType === 'worm' ? 'Royal Network Systems' : 'Royal File System'}
                    </h2>
                    
                    {malwareType === 'worm' ? (
                      <div className="grid grid-cols-4 gap-4">
                        {systems.map(system => (
                          <div
                            key={system.id}
                            className={`p-4 rounded-xl transition-all duration-500 border-2 ${
                              system.infected
                                ? 'bg-gradient-to-br from-red-600 to-orange-600 border-red-400 shadow-lg shadow-red-500/50 animate-pulse'
                                : 'bg-gradient-to-br from-indigo-800/50 to-purple-800/50 border-amber-500/30'
                            }`}
                          >
                            <div className="flex flex-col items-center">
                              <Monitor className={w-8 h-8 mb-2 ${system.infected ? 'text-white' : 'text-amber-300'}} />
                              <div className={text-sm font-bold ${system.infected ? 'text-white' : 'text-amber-200'}}>
                                {system.name}
                              </div>
                              <div className={text-xs mt-1 font-medium ${system.infected ? 'text-red-200' : 'text-purple-300'}}>
                                {system.infected ? '⚠ INFECTED' : '✓ Clean'}
                              </div>
                            </div>
                          </div>
                        ))}
                      </div>
                    ) : (
                      <div className="grid grid-cols-5 gap-3">
                        {files.map(file => (
                          <div
                            key={file.id}
                            className={`p-3 rounded-xl transition-all duration-500 border-2 ${
                              file.infected
                                ? file.disguised
                                  ? 'bg-gradient-to-br from-yellow-600 to-orange-600 border-yellow-400 shadow-lg shadow-yellow-500/50'
                                  : 'bg-gradient-to-br from-red-600 to-pink-600 border-red-400 shadow-lg shadow-red-500/50'
                                : 'bg-gradient-to-br from-indigo-800/50 to-purple-800/50 border-amber-500/30'
                            }`}
                          >
                            <div className="flex flex-col items-center">
                              <Monitor className={w-6 h-6 mb-1 ${file.infected ? 'text-white' : 'text-amber-300'}} />
                              <div className={text-xs font-bold truncate w-full text-center ${file.infected ? 'text-white' : 'text-amber-200'}}>
                                {file.name}
                              </div>
                              {file.infected && file.generation > 0 && (
                                <div className="text-xs text-red-200 mt-1 font-medium">Gen {file.generation}</div>
                              )}
                              {file.disguised && (
                                <div className="text-xs text-yellow-200 mt-1 font-bold">DISGUISED</div>
                              )}
                            </div>
                          </div>
                        ))}
                      </div>
                    )}
                  </div>

                  <div className="bg-gradient-to-br from-purple-900/80 to-indigo-900/80 backdrop-blur-lg rounded-3xl p-6 shadow-2xl border-2 border-amber-500/50">
                    <h2 className="text-2xl font-bold text-amber-300 mb-4" style={{fontFamily: 'serif'}}>Royal Activity Log</h2>
                    <div className="space-y-2 max-h-96 overflow-y-auto">
                      {logs.map(log => (
                        <div
                          key={log.id}
                          className={`p-3 rounded-xl text-sm border-2 ${
                            log.type === 'danger' ? 'bg-red-900/40 border-red-500' :
                            log.type === 'warning' ? 'bg-yellow-900/40 border-yellow-500' :
                            log.type === 'critical' ? 'bg-orange-900/40 border-orange-500' :
                            'bg-blue-900/40 border-blue-500'
                          }`}
                        >
                          <div className="text-xs text-amber-400 mb-1 font-medium">{log.timestamp}</div>
                          <div className="text-amber-100">{log.message}</div>
                        </div>
                      ))}
                    </div>
                  </div>
                </div>

                <div className="mt-6 bg-gradient-to-br from-purple-900/80 to-indigo-900/80 backdrop-blur-lg rounded-3xl p-6 shadow-2xl border-2 border-amber-500/50">
                  <h3 className="text-2xl font-bold text-amber-300 mb-4 flex items-center gap-2" style={{fontFamily: 'serif'}}>
                    <Crown className="w-6 h-6" />
                    About This Royal Simulation
                  </h3>
                  <div className="grid grid-cols-1 md:grid-cols-3 gap-4 text-sm">
                    <div className="bg-red-900/30 p-5 rounded-xl border-2 border-red-500/50">
                      <div className="font-bold text-amber-300 mb-2 text-lg" style={{fontFamily: 'serif'}}>👑 Virus</div>
                      <div className="text-amber-100">Attaches to files and replicates when files are accessed. Spreads through file execution.</div>
                    </div>
                    <div className="bg-yellow-900/30 p-5 rounded-xl border-2 border-yellow-500/50">
                      <div className="font-bold text-amber-300 mb-2 text-lg" style={{fontFamily: 'serif'}}>👑 Worm</div>
                      <div className="text-amber-100">Self-replicating malware that spreads across networks autonomously without user interaction.</div>
                    </div>
                    <div className="bg-orange-900/30 p-5 rounded-xl border-2 border-orange-500/50">
                      <div className="font-bold text-amber-300 mb-2 text-lg" style={{fontFamily: 'serif'}}>👑 Trojan</div>
                      <div className="text-amber-100">Disguises itself as legitimate software to trick users into executing malicious code.</div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<MalwareSimulator />);
    </script>
</body>
</html>
