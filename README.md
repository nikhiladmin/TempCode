```js
'use client'
import React, { useState, useRef, useEffect } from 'react';
import { FileText, UploadCloud, MessageSquareMore, UserCircle, LayoutDashboard } from 'lucide-react';

function Navbar() {
  return (
    <div className="flex items-center justify-between px-6 py-4 shadow border-b border-gray-200 bg-white">
      <div className="flex items-center gap-3">
        <LayoutDashboard className="w-6 h-6 text-blue-600" />
        <h1 className="text-xl font-semibold text-gray-800">My Dashboard</h1>
      </div>
      <div>
        <UserCircle className="w-8 h-8 text-gray-600 cursor-pointer hover:text-gray-800" />
      </div>
    </div>
  );
}

export default function Dashboard() {
  const [files, setFiles] = useState([]);
  const [selectedFile, setSelectedFile] = useState(null);
  const [messages, setMessages] = useState([]);
  const [inputMessage, setInputMessage] = useState('');
  const fileInputRef = useRef();
  const chatWindowRef = useRef(null);

  const handleFileUpload = (e) => {
    const fileList = Array.from(e.target.files);
    fileList.forEach((file) => {
      const reader = new FileReader();
      reader.onload = () => {
        setFiles((prev) => [...prev, { name: file.name, content: reader.result }]);
      };
      reader.readAsText(file);
    });
    e.target.value = null;
  };

  const openFile = (file) => {
    setSelectedFile(file);
    setMessages([]);
  };

  const handleSend = () => {
    if (!inputMessage.trim()) return;
    setMessages((prev) => [...prev, { text: inputMessage, fromUser: true }]);
    setInputMessage('');
    setTimeout(() => {
      setMessages((prev) => [...prev, { text: 'Bot response placeholder', fromUser: false }]);
    }, 500);
  };

  useEffect(() => {
    if (chatWindowRef.current) {
      chatWindowRef.current.scrollTop = chatWindowRef.current.scrollHeight;
    }
  }, [messages]);

  return (
    <div className="flex flex-col h-screen bg-white">
      {/* Navbar */}
      <Navbar />

      {/* Main Content */}
      <div className="flex flex-1 overflow-hidden">
        {/* Section 1: File Upload & List */}
        <div className="flex flex-col w-1/4 min-w-[250px] bg-white shadow-md overflow-hidden">
          <div className="p-6 border-b border-gray-200">
            <button
              className="w-full px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600 transition flex items-center justify-center gap-2"
              onClick={() => fileInputRef.current.click()}
            >
              <UploadCloud className="w-5 h-5" /> Upload File
            </button>
            <input
              type="file"
              accept="*"
              multiple
              ref={fileInputRef}
              className="hidden"
              onChange={handleFileUpload}
            />
          </div>
          <div className="flex-1 p-4 space-y-2 overflow-y-auto scrollbar-thin scrollbar-thumb-gray-300">
            {files.length === 0 && (
              <p className="text-gray-400">No files uploaded</p>
            )}
            {files.map((file, idx) => (
              <div
                key={idx}
                className={`p-2 rounded-md cursor-pointer transition flex items-center gap-2 truncate
                  ${selectedFile?.name === file.name ? 'bg-blue-100 hover:bg-blue-200' : 'bg-gray-50 hover:bg-gray-100'}`}
                onClick={() => openFile(file)}
              >
                <FileText className="w-4 h-4 text-gray-500" />
                <span className="text-gray-700 truncate">{file.name}</span>
              </div>
            ))}
          </div>
        </div>

        {/* Section 2: File Content Viewer */}
        <div className="flex-1 p-8 overflow-y-auto">
          <div className="bg-white shadow-md rounded-md h-full p-6 overflow-y-auto scrollbar-thin scrollbar-thumb-gray-300">
            {selectedFile ? (
              <>
                <h2 className="text-2xl font-semibold mb-4 text-gray-800 border-b border-gray-200 pb-2">
                  {selectedFile.name}
                </h2>
                <pre className="whitespace-pre-wrap text-sm font-mono text-gray-700">
                  {selectedFile.content}
                </pre>
              </>
            ) : (
              <div className="h-full flex items-center justify-center text-gray-400">
                Select a file to view its content
              </div>
            )}
          </div>
        </div>

        {/* Section 3: Chat Window */}
        <div className="flex flex-col w-1/4 min-w-[300px] bg-white shadow-md overflow-hidden">
          <div className="p-6 border-b border-gray-200 flex items-center gap-2">
            <MessageSquareMore className="w-5 h-5 text-gray-700" />
            <h3 className="text-lg font-medium text-gray-800">Chat</h3>
          </div>
          <div
            ref={chatWindowRef}
            className="flex-1 p-4 overflow-y-auto scrollbar-thin scrollbar-thumb-gray-300"
          >
            {messages.length === 0 && (
              <p className="text-gray-400 text-center mt-8">No messages yet</p>
            )}
            <div className="flex flex-col gap-3">
              {messages.map((msg, idx) => (
                <div
                  key={idx}
                  className={`max-w-[80%] p-3 rounded-md whitespace-pre-wrap break-words text-gray-800
                    ${msg.fromUser ? 'bg-blue-100 self-end text-right' : 'bg-gray-100 self-start text-left'}`}
                >
                  {msg.text}
                </div>
              ))}
            </div>
          </div>
          <div className="p-4 border-t border-gray-200 flex items-center">
            <input
              type="text"
              value={inputMessage}
              onChange={(e) => setInputMessage(e.target.value)}
              placeholder="Type a message..."
              className="flex-1 px-4 py-2 border border-gray-300 rounded-l-md focus:outline-none focus:ring-2 focus:ring-blue-200"
              onKeyDown={(e) => e.key === 'Enter' && handleSend()}
            />
            <button
              onClick={handleSend}
              className="ml-2 px-5 py-2 bg-green-500 text-white rounded-r-md hover:bg-green-600 transition"
            >
              Send
            </button>
          </div>
        </div>
      </div>
    </div>
  );
}
```
