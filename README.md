import React, { useState } from 'react';
import SearchFilters from './components/SearchFilters';
import ResourceCard from './components/ResourceCard';
import { fetchLearningResources } from './services/geminiService';
import { SearchParams, SearchState } from './types';

const App: React.FC = () => {
  const [params, setParams] = useState<SearchParams>({
    province: '',
    district: ''
  });

  const [searchState, setSearchState] = useState<SearchState>({
    loading: false,
    data: [],
    error: null,
    searched: false
  });

  const handleParamChange = (key: keyof SearchParams, value: string) => {
    setParams(prev => ({ ...prev, [key]: value }));
  };

  const handleSearch = async () => {
    if (!params.province) return;

    setSearchState(prev => ({ ...prev, loading: true, error: null, searched: true }));

    try {
      const data = await fetchLearningResources(params.province, params.district);
      setSearchState({
        loading: false,
        data: data,
        error: null,
        searched: true
      });
    } catch (error: any) {
      setSearchState({
        loading: false,
        data: [],
        error: error.message || 'เกิดข้อผิดพลาดในการเชื่อมต่อ',
        searched: true
      });
    }
  };

  return (
    <div className="min-h-screen flex flex-col bg-gray-50">
      {/* Header */}
      <header className="bg-thai-primary shadow-lg text-white sticky top-0 z-50">
        <div className="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-4">
          <div className="flex flex-col md:flex-row items-center justify-between">
            <div className="flex items-center space-x-4">
              <div className="bg-white/10 p-2.5 rounded-lg backdrop-blur-sm border border-white/20">
                 <svg xmlns="http://www.w3.org/2000/svg" className="h-8 w-8 text-thai-secondary" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path d="M12 14l9-5-9-5-9 5 9 5z" />
                  <path d="M12 14l6.16-3.422a12.083 12.083 0 01.665 6.479A11.952 11.952 0 0012 20.055a11.952 11.952 0 00-6.824-2.998 12.078 12.078 0 01.665-6.479L12 14z" />
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={2} d="M12 14l9-5-9-5-9 5 9 5zm0 0l6.16-3.422a12.083 12.083 0 01.665 6.479A11.952 11.952 0 0012 20.055a11.952 11.952 0 00-6.824-2.998 12.078 12.078 0 01.665-6.479L12 14zm-4 6v-7.5l4-2.222" />
                </svg>
              </div>
              <div>
                 <h1 className="text-xl md:text-2xl font-bold leading-tight">ระบบฐานข้อมูลแหล่งเรียนรู้ท้องถิ่น</h1>
                 <p className="text-sm text-teal-100 font-medium opacity-90">เชื่อมโยงมาตรฐาน/ตัวชี้วัด หลักสูตรแกนกลางฯ</p>
              </div>
            </div>
          </div>
        </div>
      </header>

      {/* Main Content */}
      <main className="flex-grow max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8 w-full">
        
        <SearchFilters 
          params={params} 
          onParamChange={handleParamChange} 
          onSearch={handleSearch}
          isLoading={searchState.loading}
        />

        {/* Results Area */}
        {searchState.error && (
          <div className="bg-red-50 border-l-4 border-red-500 p-4 rounded mb-8 animate-fade-in-up">
            <div className="flex">
              <div className="flex-shrink-0">
                <svg className="h-5 w-5 text-red-400" viewBox="0 0 20 20" fill="currentColor">
                  <path fillRule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clipRule="evenodd" />
                </svg>
              </div>
              <div className="ml-3">
                <p className="text-sm text-red-700">
                  {searchState.error}
                </p>
              </div>
            </div>
          </div>
        )}

        {searchState.loading && (
           <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 animate-pulse">
             {[1, 2, 3].map(i => (
               <div key={i} className="bg-gray-200 h-[400px] rounded-lg"></div>
             ))}
           </div>
        )}

        {!searchState.loading && searchState.searched && searchState.data.length === 0 && !searchState.error && (
          <div className="text-center py-20 bg-white rounded-xl shadow-sm border border-dashed border-gray-300">
             <div className="bg-gray-50 w-24 h-24 rounded-full flex items-center justify-center mx-auto mb-4">
               <svg xmlns="http://www.w3.org/2000/svg" className="h-10 w-10 text-gray-400" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={1} d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10" />
                </svg>
             </div>
            <p className="text-gray-600 text-lg font-medium">ไม่พบข้อมูลแหล่งเรียนรู้</p>
            <p className="text-gray-400 text-sm mt-1">ลองเปลี่ยนคำค้นหาอำเภอ หรือค้นหาเฉพาะระดับจังหวัด</p>
          </div>
        )}

        {!searchState.loading && searchState.data.length > 0 && (
          <div>
            <div className="flex items-center justify-between mb-6">
              <h3 className="text-lg font-semibold text-gray-700 border-l-4 border-thai-secondary pl-3">
                ผลการค้นหา: <span className="text-thai-primary">{searchState.data.length}</span> รายการ
              </h3>
              <span className="text-xs text-gray-500 bg-white border border-gray-200 px-3 py-1 rounded-full shadow-sm">
                AI Powered Analysis
              </span>
            </div>
            <div className="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
              {searchState.data.map((resource) => (
                <ResourceCard key={resource.id} resource={resource} />
              ))}
            </div>
          </div>
        )}

        {/* Initial Empty State */}
        {!searchState.searched && (
          <div className="text-center py-20 px-4">
            <h2 className="text-3xl md:text-4xl font-bold text-gray-800 mb-6 tracking-tight">
              ค้นหาแหล่งเรียนรู้ <span className="text-thai-primary">ใกล้ตัวคุณ</span>
            </h2>
            <p className="text-gray-600 max-w-2xl mx-auto text-lg leading-relaxed">
              ระบบช่วยค้นหาและวิเคราะห์แหล่งเรียนรู้ท้องถิ่นในทุกจังหวัด เพื่อสนับสนุนการจัดการเรียนการสอน
              ให้สอดคล้องกับบริบทชุมชนและหลักสูตรแกนกลางการศึกษาขั้นพื้นฐาน
            </p>
          </div>
        )}
      </main>

      {/* Footer */}
      <footer className="bg-gray-800 text-gray-400 py-8 mt-auto border-t border-gray-700">
        <div className="max-w-7xl mx-auto px-4 text-center">
          <p className="text-sm font-light">© {new Date().getFullYear()} ระบบฐานข้อมูลแหล่งเรียนรู้ท้องถิ่นเพื่อการศึกษาไทย</p>
          <p className="text-xs text-gray-600 mt-2">Powered by Google Gemini API</p>
        </div>
      </footer>
    </div>
  );
};

export default App;
