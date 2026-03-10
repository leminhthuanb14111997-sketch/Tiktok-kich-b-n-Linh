import { useState, useRef } from 'react';
import { generateScript } from './services/gemini';
import Markdown from 'react-markdown';
import remarkGfm from 'remark-gfm';
import { Loader2, Wand2, Copy, Check, Clapperboard, Sparkles, Video as VideoIcon, Image as ImageIcon, X } from 'lucide-react';
import { motion } from 'motion/react';

export default function App() {
  const [idea, setIdea] = useState('');
  const [productInfo, setProductInfo] = useState('');
  const [productImage, setProductImage] = useState<{data: string, mimeType: string} | null>(null);
  const [imagePreview, setImagePreview] = useState<string | null>(null);
  const [referenceVideo, setReferenceVideo] = useState<{data: string, mimeType: string} | null>(null);
  const [videoPreview, setVideoPreview] = useState<string | null>(null);
  const [script, setScript] = useState('');
  const [isLoading, setIsLoading] = useState(false);
  const [copied, setCopied] = useState(false);
  
  const fileInputRef = useRef<HTMLInputElement>(null);
  const videoInputRef = useRef<HTMLInputElement>(null);

  const handleImageUpload = (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0];
    if (file) {
      const reader = new FileReader();
      reader.onloadend = () => {
        const base64String = (reader.result as string).split(',')[1];
        setProductImage({ data: base64String, mimeType: file.type });
        setImagePreview(reader.result as string);
      };
      reader.readAsDataURL(file);
    }
  };

  const handleVideoUpload = (e: React.ChangeEvent<HTMLInputElement>) => {
    const file = e.target.files?.[0];
    if (file) {
      if (file.size > 20 * 1024 * 1024) {
        alert('Vui lòng chọn video dưới 20MB để đảm bảo tốc độ xử lý.');
        return;
      }
      const reader = new FileReader();
      reader.onloadend = () => {
        const base64String = (reader.result as string).split(',')[1];
        setReferenceVideo({ data: base64String, mimeType: file.type });
        setVideoPreview(URL.createObjectURL(file));
      };
      reader.readAsDataURL(file);
    }
  };

  const removeImage = () => {
    setProductImage(null);
    setImagePreview(null);
    if (fileInputRef.current) {
      fileInputRef.current.value = '';
    }
  };

  const removeVideo = () => {
    setReferenceVideo(null);
    if (videoPreview) {
      URL.revokeObjectURL(videoPreview);
    }
    setVideoPreview(null);
    if (videoInputRef.current) {
      videoInputRef.current.value = '';
    }
  };

  const handleGenerate = async () => {
    if (!idea.trim() && !referenceVideo && !productInfo.trim()) return;
    setIsLoading(true);
    try {
      const result = await generateScript(idea, productInfo, productImage, referenceVideo);
      setScript(result || '');
    } catch (error) {
      console.error(error);
      alert('Có lỗi xảy ra khi tạo kịch bản. Vui lòng thử lại!');
    } finally {
      setIsLoading(false);
    }
  };

  const handleCopy = () => {
    navigator.clipboard.writeText(script);
    setCopied(true);
    setTimeout(() => setCopied(false), 2000);
  };

  return (
    <div className="min-h-screen bg-zinc-950 text-zinc-50 p-4 md:p-8 font-sans selection:bg-cyan-500/30">
      <div className="max-w-6xl mx-auto space-y-8">
        {/* Header */}
        <header className="text-center space-y-4 py-8">
          <motion.div 
            initial={{ scale: 0.9, opacity: 0 }}
            animate={{ scale: 1, opacity: 1 }}
            className="inline-flex items-center justify-center p-3 bg-zinc-900 rounded-2xl border border-zinc-800 shadow-2xl mb-4"
          >
            <Clapperboard className="w-8 h-8 text-cyan-400" />
          </motion.div>
          <h1 className="text-4xl md:text-5xl font-bold tracking-tight">
            TikTok Script <span className="text-transparent bg-clip-text bg-gradient-to-r from-cyan-400 to-pink-500">Generator</span>
          </h1>
          <p className="text-zinc-400 max-w-2xl mx-auto text-lg">
            Công cụ tạo kịch bản video ngắn tự động. Chuyên trị các content "mỏ hỗn", cà khịa, bắt trend và chốt sale mượt mà.
          </p>
        </header>

        <div className="grid grid-cols-1 lg:grid-cols-12 gap-8">
          {/* Input Section */}
          <motion.div 
            initial={{ x: -20, opacity: 0 }}
            animate={{ x: 0, opacity: 1 }}
            className="lg:col-span-4 space-y-6"
          >
            <div className="bg-zinc-900/50 border border-zinc-800 rounded-2xl p-6 space-y-6 backdrop-blur-sm sticky top-8">
              
              <div className="space-y-2">
                <label className="flex items-center space-x-2 text-sm font-medium text-zinc-300">
                  <VideoIcon className="w-4 h-4 text-emerald-400" />
                  <span>Video tham khảo (Tùy chọn)</span>
                </label>
                
                {/* Video Upload */}
                <div className="mt-2">
                  {!videoPreview ? (
                    <div 
                      onClick={() => videoInputRef.current?.click()}
                      className="w-full h-24 border-2 border-dashed border-zinc-800 hover:border-emerald-500/50 rounded-xl flex flex-col items-center justify-center cursor-pointer transition-colors bg-zinc-950"
                    >
                      <VideoIcon className="w-6 h-6 text-zinc-500 mb-2" />
                      <span className="text-xs text-zinc-500">Tải video lên (Tối đa 20MB)</span>
                    </div>
                  ) : (
                    <div className="relative w-full h-40 rounded-xl border border-zinc-800 overflow-hidden bg-zinc-950">
                      <video src={videoPreview} controls className="w-full h-full object-contain" />
                      <button 
                        onClick={removeVideo}
                        className="absolute top-2 right-2 p-1 bg-zinc-900/80 hover:bg-red-500/80 text-white rounded-full backdrop-blur-sm transition-colors z-10"
                      >
                        <X className="w-4 h-4" />
                      </button>
                    </div>
                  )}
                  <input 
                    type="file" 
                    ref={videoInputRef} 
                    onChange={handleVideoUpload} 
                    accept="video/*" 
                    className="hidden" 
                  />
                </div>
              </div>

              <div className="space-y-2">
                <label className="flex items-center space-x-2 text-sm font-medium text-zinc-300">
                  <Sparkles className="w-4 h-4 text-pink-400" />
                  <span>Ý tưởng của bạn</span>
                </label>
                <textarea
                  value={idea}
                  onChange={(e) => setIdea(e.target.value)}
                  placeholder="VD: Sếp ép KPI đại lý, Sale khóc lóc ăn vạ, Content đứng quay video cười cợt..."
                  className="w-full h-32 bg-zinc-950 border border-zinc-800 rounded-xl p-3 text-sm focus:ring-2 focus:ring-cyan-500/50 focus:border-cyan-500 outline-none transition-all resize-none placeholder:text-zinc-600"
                />
              </div>

              <div className="space-y-2">
                <label className="flex items-center space-x-2 text-sm font-medium text-zinc-300">
                  <ImageIcon className="w-4 h-4 text-cyan-400" />
                  <span>Sản phẩm / Đạo cụ (Tùy chọn)</span>
                </label>
                
                {/* Image Upload */}
                <div className="mt-2">
                  {!imagePreview ? (
                    <div 
                      onClick={() => fileInputRef.current?.click()}
                      className="w-full h-24 border-2 border-dashed border-zinc-800 hover:border-cyan-500/50 rounded-xl flex flex-col items-center justify-center cursor-pointer transition-colors bg-zinc-950"
                    >
                      <ImageIcon className="w-6 h-6 text-zinc-500 mb-2" />
                      <span className="text-xs text-zinc-500">Tải ảnh sản phẩm/đạo cụ lên (Tùy chọn)</span>
                    </div>
                  ) : (
                    <div className="relative w-full h-32 rounded-xl border border-zinc-800 overflow-hidden bg-zinc-950">
                      <img src={imagePreview} alt="Preview" className="w-full h-full object-contain" />
                      <button 
                        onClick={removeImage}
                        className="absolute top-2 right-2 p-1 bg-zinc-900/80 hover:bg-red-500/80 text-white rounded-full backdrop-blur-sm transition-colors"
                      >
                        <X className="w-4 h-4" />
                      </button>
                    </div>
                  )}
                  <input 
                    type="file" 
                    ref={fileInputRef} 
                    onChange={handleImageUpload} 
                    accept="image/*" 
                    className="hidden" 
                  />
                </div>

                <textarea
                  value={productInfo}
                  onChange={(e) => setProductInfo(e.target.value)}
                  placeholder="Nhập thông tin sản phẩm/đạo cụ (VD: Laptop công ty, xấp tài liệu, hoặc chậu cây...)"
                  className="w-full h-24 bg-zinc-950 border border-zinc-800 rounded-xl p-3 text-sm focus:ring-2 focus:ring-cyan-500/50 focus:border-cyan-500 outline-none transition-all resize-none placeholder:text-zinc-600 mt-3"
                />
              </div>

              <button
                onClick={handleGenerate}
                disabled={isLoading || (!idea.trim() && !referenceVideo && !productInfo.trim())}
                className="w-full bg-gradient-to-r from-cyan-500 to-pink-500 hover:from-cyan-400 hover:to-pink-400 text-white font-semibold py-3 px-4 rounded-xl flex items-center justify-center space-x-2 transition-all disabled:opacity-50 disabled:cursor-not-allowed shadow-lg shadow-pink-500/20"
              >
                {isLoading ? (
                  <>
                    <Loader2 className="w-5 h-5 animate-spin" />
                    <span>Đang rặn kịch bản...</span>
                  </>
                ) : (
                  <>
                    <Wand2 className="w-5 h-5" />
                    <span>Tạo Kịch Bản Ngay</span>
                  </>
                )}
              </button>
            </div>
          </motion.div>

          {/* Output Section */}
          <motion.div 
            initial={{ x: 20, opacity: 0 }}
            animate={{ x: 0, opacity: 1 }}
            className="lg:col-span-8"
          >
            <div className="bg-zinc-900/50 border border-zinc-800 rounded-2xl p-6 min-h-[600px] backdrop-blur-sm flex flex-col">
              <div className="flex items-center justify-between mb-6 pb-4 border-b border-zinc-800">
                <h2 className="text-lg font-semibold text-zinc-100 flex items-center">
                  <Clapperboard className="w-5 h-5 mr-2 text-cyan-400" />
                  Kịch Bản Đầu Ra
                </h2>
                {script && (
                  <button
                    onClick={handleCopy}
                    className="flex items-center space-x-2 text-sm bg-zinc-800 hover:bg-zinc-700 text-zinc-300 py-1.5 px-3 rounded-lg transition-colors"
                  >
                    {copied ? <Check className="w-4 h-4 text-emerald-400" /> : <Copy className="w-4 h-4" />}
                    <span>{copied ? 'Đã copy' : 'Copy'}</span>
                  </button>
                )}
              </div>

              <div className="flex-1 overflow-auto">
                {isLoading ? (
                  <div className="h-full flex flex-col items-center justify-center text-zinc-500 space-y-4 py-20">
                    <Loader2 className="w-8 h-8 animate-spin text-cyan-500" />
                    <p className="animate-pulse">Đang phân tích ý tưởng và viết thoại mỏ hỗn...</p>
                  </div>
                ) : script ? (
                  <div className="markdown-body text-zinc-300">
                    <Markdown remarkPlugins={[remarkGfm]}>{script}</Markdown>
                  </div>
                ) : (
                  <div className="h-full flex flex-col items-center justify-center text-zinc-600 space-y-4 py-20">
                    <div className="w-16 h-16 rounded-full bg-zinc-800/50 flex items-center justify-center">
                      <Wand2 className="w-8 h-8 text-zinc-700" />
                    </div>
                    <p>Nhập ý tưởng hoặc tải video tham khảo bên trái và bấm tạo để xem kết quả</p>
                  </div>
                )}
              </div>
            </div>
          </motion.div>
        </div>
      </div>
    </div>
  );
}
