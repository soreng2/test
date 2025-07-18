import React, { useState, useEffect, useRef } from 'react';
import { Camera, Upload, Play, Pause, Square, Target, BarChart3, Home, Settings, LogOut, Plus, Trash2 } from 'lucide-react';

const GRIDA = () => {
  const [currentScreen, setCurrentScreen] = useState('home');
  const [user, setUser] = useState(null);
  const [timeLeft, setTimeLeft] = useState(1500);
  const [customTime, setCustomTime] = useState(25);
  const [isRunning, setIsRunning] = useState(false);
  const [selectedBgm, setSelectedBgm] = useState('none');
  const [currentSession, setCurrentSession] = useState(null);
  const [artwork, setArtwork] = useState(null);
  const [emotion, setEmotion] = useState('');
  const [focus, setFocus] = useState(3);
  const [memo, setMemo] = useState('');
  const [showOnboarding, setShowOnboarding] = useState(true);
  const [onboardingStep, setOnboardingStep] = useState(0);
  const [showLogoutConfirm, setShowLogoutConfirm] = useState(false);
  const [goalTrackers, setGoalTrackers] = useState([]);
  const [activeTracker, setActiveTracker] = useState(null);
  const [showCreateTracker, setShowCreateTracker] = useState(false);
  const [badges, setBadges] = useState([]);
  
  const fileInputRef = useRef(null);
  const cameraInputRef = useRef(null);

  const APP_VERSION = "1.0.0";

  const motivationMessages = [
    "오늘의 한 획이 내일의 작품을 만듭니다.",
    "매일 그리는 당신이 멋져요!",
    "한 장의 그림으로 하루를 채우세요.",
    "꾸준함이 곧 실력입니다.",
    "오늘도 끝까지 집중해 보세요.",
    "작은 스케치가 큰 변화를 만듭니다.",
    "하루의 시작은 드로잉으로!",
    "그려보면 실력이 쑥 늘게 될거예요!",
    "실수도 작품의 일부입니다!",
    "펜을 든 순간이 이미 성공입니다.",
    "매일의 습관이 나를 키웁니다.",
    "그림은 마음을 비추는 거울입니다.",
    "당신의 색을 믿으세요.",
    "꾸준한 연습이 최고의 선생님입니다.",
    "5분이라도 그리면, 당신은 이미 아티스트.",
    "반복이 천재를 이깁니다.",
    "그림은 꾸준함의 결과물입니다.",
    "그림은 당신의 목소리입니다.",
    "드로잉은 마음의 운동입니다.",
    "작은 단계가 큰 변화를 만듭니다.",
    "매일 그림 그리기는 나를 위한 약속.",
    "매일의 습관이 나를 빛나게 합니다.",
    "그림은 하루를 특별하게 만듭니다.",
    "꾸준함이 당신을 프로로 만듭니다.",
    "그림은 연습에서 시작됩니다.",
    "붓을 든 순간, 이미 예술가.",
    "매일 그림 그리기는 나를 돌보는 시간.",
    "꾸준히 그리는 당신에게 박수를!",
    "반복은 예술의 어머니.",
    "그림으로 나를 표현해봐요.",
    "매일 연습, 매일 발전.",
    "그림은 꾸준함의 열매입니다.",
    "매일의 드로잉이 나를 만듭니다.",
    "그림은 나를 위로하는 언어.",
    "꾸준히 그리는 당신, 최고입니다.",
    "오늘 그린 그림이 내일의 희망이 됩니다."
  ];

  const bgmOptions = [
    { id: 'none', name: '없음', icon: '🔇' },
    { id: 'forest', name: '숲소리', icon: '🌲' },
    { id: 'rain', name: '빗소리', icon: '🌧️' },
    { id: 'cafe', name: '카페', icon: '☕' },
    { id: 'piano', name: '피아노', icon: '🎹' }
  ];

  const emotionOptions = [
    { id: 'excited', emoji: '🤩', label: '신나요' },
    { id: 'happy', emoji: '😊', label: '좋아요' },
    { id: 'neutral', emoji: '😐', label: '보통' },
    { id: 'tired', emoji: '😴', label: '피곤해요' },
    { id: 'frustrated', emoji: '😤', label: '답답해요' }
  ];

  const badgeDefinitions = [
    { id: 'first_draw', name: '첫 드로잉', icon: '🎨', description: '첫 드로잉 완료' },
    { id: 'focus_master', name: '집중력 마스터', icon: '🧠', description: '집중도 5점 달성' },
    { id: 'speed_demon', name: '스피드 드로잉', icon: '⚡', description: '5분 이내 드로잉' },
    { id: 'goal_10', name: '목표 달성자', icon: '🏆', description: '10회 목표 달성' },
    { id: 'goal_30', name: '월간 챌린저', icon: '🥇', description: '30회 목표 달성' },
    { id: 'emotion_happy', name: '해피 드로잉', icon: '😊', description: '즐거운 감정으로 드로잉' },
    { id: 'long_session', name: '인내의 화가', icon: '🎭', description: '60분 이상 드로잉' }
  ];

  const getTodayMotivation = () => {
    const today = new Date();
    const dayOfYear = Math.floor((today - new Date(today.getFullYear(), 0, 0)) / (1000 * 60 * 60 * 24));
    return motivationMessages[dayOfYear % motivationMessages.length];
  };

  useEffect(() => {
    const savedData = localStorage.getItem('gridaData');
    if (savedData) {
      try {
        const data = JSON.parse(savedData);
        setUser(data.user || null);
        setGoalTrackers(data.goalTrackers || []);
        setActiveTracker(data.activeTracker || null);
        setBadges(data.badges || []);
        setShowOnboarding(!data.user);
      } catch (e) {
        console.error('데이터 로드 오류:', e);
      }
    }
  }, []);

  useEffect(() => {
    if (isRunning && timeLeft > 0) {
      const interval = setInterval(() => {
        setTimeLeft(prev => {
          if (prev <= 1) {
            setIsRunning(false);
            handleTimerComplete();
            return 0;
          }
          return prev - 1;
        });
      }, 1000);
      return () => clearInterval(interval);
    }
  }, [isRunning, timeLeft]);

  const handleTimerComplete = () => {
    if (Notification.permission === 'granted') {
      new Notification('드로잉 타이머 완료!', {
        body: '수고하셨습니다! 작품을 등록해보세요.',
        icon: '🎨'
      });
    }
    
    const session = {
      date: new Date().toISOString().split('T')[0],
      duration: customTime - Math.floor(timeLeft / 60),
      startTime: new Date().toISOString(),
      bgm: selectedBgm
    };
    setCurrentSession(session);
    setCurrentScreen('artwork');
  };

  const toggleTimer = () => {
    if (!isRunning && Notification.permission === 'default') {
      Notification.requestPermission();
    }
    setIsRunning(!isRunning);
  };

  const resetTimer = () => {
    setIsRunning(false);
    setTimeLeft(customTime * 60);
  };

  const applyCustomTime = () => {
    if (!isRunning) {
      setTimeLeft(customTime * 60);
    }
  };

  const formatTime = (seconds) => {
    const mins = Math.floor(seconds / 60);
    const secs = seconds % 60;
    return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
  };

  const handleImageUpload = (event) => {
    const file = event.target.files[0];
    if (file) {
      const reader = new FileReader();
      reader.onload = (e) => {
        const img = new Image();
        img.onload = () => {
          const canvas = document.createElement('canvas');
          const ctx = canvas.getContext('2d');
          
          const maxSize = 800;
          let { width, height } = img;
          
          if (width > height) {
            if (width > maxSize) {
              height = (height * maxSize) / width;
              width = maxSize;
            }
          } else {
            if (height > maxSize) {
              width = (width * maxSize) / height;
              height = maxSize;
            }
          }
          
          canvas.width = width;
          canvas.height = height;
          ctx.drawImage(img, 0, 0, width, height);
          
          const resizedDataUrl = canvas.toDataURL('image/jpeg', 0.8);
          setArtwork(resizedDataUrl);
        };
        img.src = e.target.result;
      };
      reader.readAsDataURL(file);
    }
  };

  const saveSession = () => {
    if (!currentSession) return;
    
    const sessionData = {
      ...currentSession,
      emotion,
      focus,
      memo,
      artwork
    };
    
    const existingData = JSON.parse(localStorage.getItem('gridaData') || '{}');
    if (!existingData.sessions) existingData.sessions = {};
    
    existingData.sessions[currentSession.date] = sessionData;
    
    // 활성 트래커 업데이트
    if (activeTracker) {
      const updatedTrackers = goalTrackers.map(tracker => 
        tracker.id === activeTracker.id 
          ? { ...tracker, completed: Math.min(tracker.completed + 1, tracker.target) }
          : tracker
      );
      setGoalTrackers(updatedTrackers);
      existingData.goalTrackers = updatedTrackers;
    }
    
    // 뱃지 체크
    const newBadges = checkBadges(sessionData, existingData);
    setBadges(newBadges);
    existingData.badges = newBadges;
    
    localStorage.setItem('gridaData', JSON.stringify(existingData));
    
    setCurrentSession(null);
    setArtwork(null);
    setEmotion('');
    setFocus(3);
    setMemo('');
    resetTimer();
    setCurrentScreen('home');
  };

  const checkBadges = (sessionData, existingData) => {
    const currentBadges = existingData.badges || [];
    const newBadges = [...currentBadges];
    const sessions = existingData.sessions || {};
    
    // 첫 드로잉 뱃지
    if (!newBadges.some(b => b.id === 'first_draw') && Object.keys(sessions).length >= 1) {
      newBadges.push({ id: 'first_draw', earnedAt: new Date().toISOString() });
    }
    
    // 집중력 마스터 뱃지
    if (!newBadges.some(b => b.id === 'focus_master') && sessionData.focus === 5) {
      newBadges.push({ id: 'focus_master', earnedAt: new Date().toISOString() });
    }
    
    // 스피드 드로잉 뱃지
    if (!newBadges.some(b => b.id === 'speed_demon') && sessionData.duration <= 5) {
      newBadges.push({ id: 'speed_demon', earnedAt: new Date().toISOString() });
    }
    
    // 장시간 드로잉 뱃지
    if (!newBadges.some(b => b.id === 'long_session') && sessionData.duration >= 60) {
      newBadges.push({ id: 'long_session', earnedAt: new Date().toISOString() });
    }
    
    // 해피 드로잉 뱃지
    if (!newBadges.some(b => b.id === 'emotion_happy') && sessionData.emotion === 'happy') {
      newBadges.push({ id: 'emotion_happy', earnedAt: new Date().toISOString() });
    }
    
    // 목표 달성 뱃지들
    const totalSessions = Object.keys(sessions).length;
    if (!newBadges.some(b => b.id === 'goal_10') && totalSessions >= 10) {
      newBadges.push({ id: 'goal_10', earnedAt: new Date().toISOString() });
    }
    if (!newBadges.some(b => b.id === 'goal_30') && totalSessions >= 30) {
      newBadges.push({ id: 'goal_30', earnedAt: new Date().toISOString() });
    }
    
    return newBadges;
  };

  const createGoalTracker = (name, target, description) => {
    const newTracker = {
      id: Date.now().toString(),
      name,
      target,
      description,
      completed: 0,
      createdAt: new Date().toISOString()
    };
    
    const updatedTrackers = [...goalTrackers, newTracker];
    setGoalTrackers(updatedTrackers);
    
    if (goalTrackers.length === 0) {
      setActiveTracker(newTracker);
    }
    
    const existingData = JSON.parse(localStorage.getItem('gridaData') || '{}');
    existingData.goalTrackers = updatedTrackers;
    if (goalTrackers.length === 0) {
      existingData.activeTracker = newTracker;
    }
    localStorage.setItem('gridaData', JSON.stringify(existingData));
    
    setShowCreateTracker(false);
  };

  const deleteTracker = (trackerId) => {
    const updatedTrackers = goalTrackers.filter(t => t.id !== trackerId);
    setGoalTrackers(updatedTrackers);
    
    if (activeTracker?.id === trackerId) {
      setActiveTracker(updatedTrackers[0] || null);
    }
    
    const existingData = JSON.parse(localStorage.getItem('gridaData') || '{}');
    existingData.goalTrackers = updatedTrackers;
    existingData.activeTracker = activeTracker?.id === trackerId ? (updatedTrackers[0] || null) : activeTracker;
    localStorage.setItem('gridaData', JSON.stringify(existingData));
  };

  const completeOnboarding = (userData) => {
    setUser(userData);
    setShowOnboarding(false);
    
    const data = {
      user: userData,
      goalTrackers: [],
      activeTracker: null,
      badges: [],
      settings: {}
    };
    localStorage.setItem('gridaData', JSON.stringify(data));
    setCurrentScreen('home');
  };

  const handleLogout = () => {
    localStorage.removeItem('gridaData');
    setUser(null);
    setShowOnboarding(true);
    setOnboardingStep(0);
    setCurrentScreen('home');
    setShowLogoutConfirm(false);
    setTimeLeft(1500);
    setCustomTime(25);
    setIsRunning(false);
    setSelectedBgm('none');
    setCurrentSession(null);
    setArtwork(null);
    setEmotion('');
    setFocus(3);
    setMemo('');
    setGoalTrackers([]);
    setActiveTracker(null);
    setBadges([]);
  };

  const getCalendarData = () => {
    const data = JSON.parse(localStorage.getItem('gridaData') || '{}');
    return data.sessions || {};
  };

  const getReportData = () => {
    const sessions = getCalendarData();
    const thisWeek = Object.values(sessions).filter(session => {
      const sessionDate = new Date(session.date);
      const today = new Date();
      const weekStart = new Date(today.setDate(today.getDate() - today.getDay()));
      return sessionDate >= weekStart;
    });
    
    return {
      totalTime: thisWeek.reduce((acc, session) => acc + (session.duration || 0), 0),
      avgFocus: thisWeek.length > 0 ? thisWeek.reduce((acc, session) => acc + (session.focus || 3), 0) / thisWeek.length : 0,
      completedDays: thisWeek.length,
      emotions: thisWeek.reduce((acc, session) => {
        if (session.emotion) {
          acc[session.emotion] = (acc[session.emotion] || 0) + 1;
        }
        return acc;
      }, {})
    };
  };

  if (showOnboarding) {
    return (
      <div className="min-h-screen bg-gradient-to-br from-purple-50 to-pink-50 p-4">
        <div className="max-w-md mx-auto">
          {onboardingStep === 0 && (
            <div className="text-center py-20">
              <div className="text-6xl mb-6">🎨</div>
              <h1 className="text-3xl font-bold text-gray-800 mb-4">GRIDA</h1>
              <p className="text-gray-600 mb-8">매일 그림을 그리는 습관을 만들어보세요</p>
              <button
                onClick={() => setOnboardingStep(1)}
                className="bg-purple-500 text-white px-8 py-3 rounded-full font-medium hover:bg-purple-600 transition-colors"
              >
                시작하기
              </button>
            </div>
          )}
          
          {onboardingStep === 1 && (
            <div className="py-10">
              <h2 className="text-2xl font-bold text-gray-800 mb-6 text-center">프로필 설정</h2>
              <div className="space-y-4">
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">
                    닉네임
                  </label>
                  <input
                    type="text"
                    placeholder="드로잉 러버"
                    id="nickname-input"
                    className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-transparent"
                    onKeyPress={(e) => {
                      if (e.key === 'Enter') {
                        const nickname = e.target.value.trim() || '드로잉 러버';
                        completeOnboarding({ nickname });
                      }
                    }}
                  />
                </div>
                
                <button
                  onClick={() => {
                    const nicknameInput = document.getElementById('nickname-input');
                    const nickname = nicknameInput?.value.trim() || '드로잉 러버';
                    completeOnboarding({ nickname });
                  }}
                  className="w-full bg-purple-500 text-white py-3 rounded-lg font-medium hover:bg-purple-600 transition-colors"
                >
                  시작하기
                </button>
              </div>
            </div>
          )}
        </div>
      </div>
    );
  }

  return (
    <div className="min-h-screen bg-gradient-to-br from-purple-50 to-pink-50 pb-20">
      <header className="bg-white shadow-sm p-4">
        <div className="max-w-md mx-auto flex items-center justify-between">
          <h1 className="text-xl font-bold text-gray-800">GRIDA</h1>
          <div className="flex items-center space-x-3">
            <span className="text-sm text-gray-600">안녕하세요, {user?.nickname}님!</span>
            <button
              onClick={() => setCurrentScreen('settings')}
              className="p-2 text-gray-500 hover:text-gray-700 transition-colors"
            >
              <Settings size={20} />
            </button>
          </div>
        </div>
      </header>

      <div className="max-w-md mx-auto p-4">
        {currentScreen === 'home' && (
          <div className="space-y-6">
            <div className="bg-white rounded-2xl p-6 shadow-sm">
              <div className="text-center">
                <div className="text-4xl mb-2">🎨</div>
                <h2 className="text-lg font-semibold text-gray-800">
                  {getTodayMotivation()}
                </h2>
                <p className="text-sm text-gray-600 mt-1">
                  {new Date().toLocaleDateString('ko-KR', { 
                    month: 'long', 
                    day: 'numeric',
                    weekday: 'long' 
                  })}
                </p>
              </div>
            </div>

            <div className="bg-white rounded-2xl p-6 shadow-sm">
              <div className="text-center">
                <div className="mb-6">
                  <label className="block text-sm font-medium text-gray-700 mb-3">
                    드로잉 시간 설정
                  </label>
                  <input
                    type="range"
                    min="5"
                    max="120"
                    value={customTime}
                    onChange={(e) => setCustomTime(Number(e.target.value))}
                    onMouseUp={applyCustomTime}
                    onTouchEnd={applyCustomTime}
                    disabled={isRunning}
                    className="w-full h-2 bg-purple-200 rounded-lg appearance-none cursor-pointer disabled:opacity-50"
                  />
                  <div className="flex justify-between text-xs text-gray-500 mt-2">
                    <span>5분</span>
                    <span className="font-semibold text-purple-600">{customTime}분</span>
                    <span>2시간</span>
                  </div>
                  {isRunning && (
                    <p className="text-xs text-orange-600 mt-1">
                      * 타이머 실행 중에는 시간을 변경할 수 없습니다
                    </p>
                  )}
                </div>

                <div className="text-4xl font-bold text-purple-600 mb-4">
                  {formatTime(timeLeft)}
                </div>
                <div className="flex justify-center space-x-4 mb-4">
                  <button
                    onClick={toggleTimer}
                    className={`flex items-center space-x-2 px-6 py-3 rounded-full font-medium transition-colors ${
                      isRunning
                        ? 'bg-red-500 text-white hover:bg-red-600'
                        : 'bg-purple-500 text-white hover:bg-purple-600'
                    }`}
                  >
                    {isRunning ? <Pause size={20} /> : <Play size={20} />}
                    <span>{isRunning ? '일시정지' : '시작'}</span>
                  </button>
                  <button
                    onClick={resetTimer}
                    className="flex items-center space-x-2 px-6 py-3 rounded-full border border-gray-300 text-gray-700 hover:bg-gray-50 transition-colors"
                  >
                    <Square size={20} />
                    <span>리셋</span>
                  </button>
                </div>
              </div>
            </div>

            <div className="bg-white rounded-2xl p-6 shadow-sm">
              <h3 className="text-lg font-semibold text-gray-800 mb-4">배경음악</h3>
              <div className="grid grid-cols-3 gap-2">
                {bgmOptions.map((bgm) => (
                  <button
                    key={bgm.id}
                    onClick={() => setSelectedBgm(bgm.id)}
                    className={`p-3 rounded-lg text-sm font-medium transition-colors ${
                      selectedBgm === bgm.id
                        ? 'bg-purple-500 text-white'
                        : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
                    }`}
                  >
                    <div className="text-lg mb-1">{bgm.icon}</div>
                    <div>{bgm.name}</div>
                  </button>
                ))}
              </div>
            </div>

            {activeTracker && (
              <div className="bg-white rounded-2xl p-6 shadow-sm">
                <div className="flex items-center justify-between mb-4">
                  <div>
                    <h3 className="text-lg font-semibold text-gray-800">{activeTracker.name}</h3>
                    <p className="text-sm text-gray-600">{activeTracker.description}</p>
                  </div>
                  <div className="text-right">
                    <div className="text-2xl font-bold text-purple-600">
                      {activeTracker.completed}/{activeTracker.target}
                    </div>
                    <div className="text-xs text-gray-500">
                      {Math.round((activeTracker.completed / activeTracker.target) * 100)}%
                    </div>
                  </div>
                </div>
                <div className="w-full bg-gray-200 rounded-full h-2">
                  <div 
                    className="bg-purple-500 h-2 rounded-full transition-all duration-300"
                    style={{ width: `${Math.min((activeTracker.completed / activeTracker.target) * 100, 100)}%` }}
                  ></div>
                </div>
              </div>
            )}

            {badges.length > 0 && (
              <div className="bg-white rounded-2xl p-6 shadow-sm">
                <h3 className="text-lg font-semibold text-gray-800 mb-4">최근 획득 뱃지</h3>
                <div className="flex space-x-3 overflow-x-auto">
                  {badges.slice(-4).map((badge) => {
                    const badgeInfo = badgeDefinitions.find(b => b.id === badge.id);
                    return (
                      <div key={badge.id} className="flex-shrink-0 text-center">
                        <div className="w-12 h-12 bg-yellow-100 rounded-full flex items-center justify-center mb-2">
                          <span className="text-xl">{badgeInfo?.icon}</span>
                        </div>
                        <div className="text-xs text-gray-600 w-16">{badgeInfo?.name}</div>
                      </div>
                    );
                  })}
                </div>
              </div>
            )}
          </div>
        )}

        {currentScreen === 'artwork' && (
          <div className="space-y-6">
            <div className="bg-white rounded-2xl p-6 shadow-sm">
              <h2 className="text-xl font-bold text-gray-800 mb-4 text-center">작품 등록</h2>
              
              {!artwork ? (
                <div className="space-y-4">
                  <div className="border-2 border-dashed border-gray-300 rounded-lg p-8 text-center">
                    <div className="text-4xl mb-4">📸</div>
                    <p className="text-gray-600 mb-4">오늘 그린 작품을 등록해보세요!</p>
                    <div className="space-y-3">
                      <button
                        onClick={() => cameraInputRef.current?.click()}
                        className="w-full flex items-center justify-center space-x-2 bg-purple-500 text-white py-3 rounded-lg hover:bg-purple-600 transition-colors"
                      >
                        <Camera size={20} />
                        <span>사진 촬영</span>
                      </button>
                      <button
                        onClick={() => fileInputRef.current?.click()}
                        className="w-full flex items-center justify-center space-x-2 bg-gray-500 text-white py-3 rounded-lg hover:bg-gray-600 transition-colors"
                      >
                        <Upload size={20} />
                        <span>갤러리에서 선택</span>
                      </button>
                      <button
                        onClick={() => setCurrentScreen('emotion')}
                        className="w-full text-gray-500 py-2 hover:text-gray-700 transition-colors"
                      >
                        건너뛰기
                      </button>
                    </div>
                  </div>
                  
                  <input
                    ref={cameraInputRef}
                    type="file"
                    accept="image/*"
                    capture="environment"
                    onChange={handleImageUpload}
                    className="hidden"
                  />
                  <input
                    ref={fileInputRef}
                    type="file"
                    accept="image/*"
                    onChange={handleImageUpload}
                    className="hidden"
                  />
                </div>
              ) : (
                <div className="space-y-4">
                  <div className="rounded-lg overflow-hidden">
                    <img 
                      src={artwork} 
                      alt="등록된 작품" 
                      className="w-full h-64 object-cover"
                    />
                  </div>
                  <div className="flex space-x-3">
                    <button
                      onClick={() => setArtwork(null)}
                      className="flex-1 bg-gray-500 text-white py-3 rounded-lg hover:bg-gray-600 transition-colors"
                    >
                      다시 선택
                    </button>
                    <button
                      onClick={() => setCurrentScreen('emotion')}
                      className="flex-1 bg-purple-500 text-white py-3 rounded-lg hover:bg-purple-600 transition-colors"
                    >
                      다음
                    </button>
                  </div>
                </div>
              )}
            </div>
          </div>
        )}

        {currentScreen === 'emotion' && (
          <div className="space-y-6">
            <div className="bg-white rounded-2xl p-6 shadow-sm">
              <h2 className="text-xl font-bold text-gray-800 mb-6 text-center">
                오늘의 드로잉은 어떠셨나요?
              </h2>
              
              <div className="mb-6">
                <h3 className="text-lg font-semibold text-gray-800 mb-4">감정</h3>
                <div className="grid grid-cols-3 gap-3">
                  {emotionOptions.map((option) => (
                    <button
                      key={option.id}
                      onClick={() => setEmotion(option.id)}
                      className={`p-4 rounded-lg text-center transition-all ${
                        emotion === option.id
                          ? 'bg-purple-500 text-white shadow-lg transform scale-105'
                          : 'bg-gray-100 text-gray-700 hover:bg-gray-200'
                      }`}
                    >
                      <div className="text-2xl mb-2">{option.emoji}</div>
                      <div className="text-sm font-medium">{option.label}</div>
                    </button>
                  ))}
                </div>
              </div>

              <div className="mb-6">
                <h3 className="text-lg font-semibold text-gray-800 mb-4">집중도</h3>
                <div className="px-4">
                  <input
                    type="range"
                    min="1"
                    max="5"
                    value={focus}
                    onChange={(e) => setFocus(Number(e.target.value))}
                    className="w-full h-2 bg-gray-200 rounded-lg appearance-none cursor-pointer"
                  />
                  <div className="flex justify-between text-sm text-gray-600 mt-2">
                    <span>낮음</span>
                    <span className="font-semibold text-purple-600">{focus}/5</span>
                    <span>높음</span>
                  </div>
                </div>
              </div>

              <div className="mb-6">
                <h3 className="text-lg font-semibold text-gray-800 mb-4">메모 (선택)</h3>
                <textarea
                  value={memo}
                  onChange={(e) => setMemo(e.target.value)}
                  placeholder="오늘의 드로잉에 대한 생각을 기록해보세요..."
                  className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-transparent resize-none"
                  rows="4"
                  maxLength="300"
                />
                <div className="text-right text-sm text-gray-500 mt-1">
                  {memo.length}/300
                </div>
              </div>

              <button
                onClick={saveSession}
                disabled={!emotion}
                className={`w-full py-4 rounded-lg font-medium transition-colors ${
                  emotion
                    ? 'bg-purple-500 text-white hover:bg-purple-600'
                    : 'bg-gray-300 text-gray-500 cursor-not-allowed'
                }`}
              >
                저장하기
              </button>
            </div>
          </div>
        )}

        {currentScreen === 'goals' && (
          <div className="space-y-6">
            <div className="bg-white rounded-2xl p-6 shadow-sm">
              <div className="flex items-center justify-between mb-6">
                <h2 className="text-xl font-bold text-gray-800">목표 트래커</h2>
                <button
                  onClick={() => setShowCreateTracker(true)}
                  className="bg-purple-500 text-white px-4 py-2 rounded-lg text-sm hover:bg-purple-600 transition-colors"
                >
                  + 새 목표
                </button>
              </div>
              
              {goalTrackers.length === 0 ? (
                <div className="text-center py-8">
                  <div className="text-4xl mb-4">🎯</div>
                  <p className="text-gray-600 mb-4">아직 목표가 없습니다</p>
                  <button
                    onClick={() => setShowCreateTracker(true)}
                    className="bg-purple-500 text-white px-6 py-3 rounded-lg hover:bg-purple-600 transition-colors"
                  >
                    첫 목표 만들기
                  </button>
                </div>
              ) : (
                <div className="space-y-4">
                  {goalTrackers.map((tracker) => (
                    <div 
                      key={tracker.id}
                      className={`p-4 rounded-lg border-2 transition-all cursor-pointer ${
                        activeTracker?.id === tracker.id 
                          ? 'border-purple-500 bg-purple-50' 
                          : 'border-gray-200 hover:border-gray-300'
                      }`}
                      onClick={() => {
                        setActiveTracker(tracker);
                        const existingData = JSON.parse(localStorage.getItem('gridaData') || '{}');
                        existingData.activeTracker = tracker;
                        localStorage.setItem('gridaData', JSON.stringify(existingData));
                      }}
                    >
                      <div className="flex items-center justify-between mb-3">
                        <div>
                          <h3 className="font-semibold text-gray-800">{tracker.name}</h3>
                          <p className="text-sm text-gray-600">{tracker.description}</p>
                        </div>
                        <div className="flex items-center space-x-2">
                          <span className="text-sm font-medium text-purple-600">
                            {tracker.completed}/{tracker.target}
                          </span>
                          <button
                            onClick={(e) => {
                              e.stopPropagation();
                              deleteTracker(tracker.id);
                            }}
                            className="text-red-500 hover:text-red-700 p-1"
                          >
                            <Trash2 size={16} />
                          </button>
                        </div>
                      </div>
                      
                      <div className="w-full bg-gray-200 rounded-full h-2 mb-3">
                        <div 
                          className="bg-purple-500 h-2 rounded-full transition-all duration-300"
                          style={{ width: `${Math.min((tracker.completed / tracker.target) * 100, 100)}%` }}
                        ></div>
                      </div>
                      
                      {tracker.completed >= tracker.target && (
                        <div className="text-center">
                          <span className="text-2xl">🎉</span>
                          <span className="text-sm text-green-600 ml-2">목표 달성!</span>
                        </div>
                      )}
                    </div>
                  ))}
                </div>
              )}
            </div>

            <div className="bg-white rounded-2xl p-6 shadow-sm">
              <h3 className="text-lg font-semibold text-gray-800 mb-4">뱃지 컬렉션</h3>
              <div className="grid grid-cols-3 gap-4">
                {badgeDefinitions.map((badgeInfo) => {
                  const earned = badges.some(b => b.id === badgeInfo.id);
                  return (
                    <div 
                      key={badgeInfo.id}
                      className={`text-center p-3 rounded-lg transition-all ${
                        earned ? 'bg-yellow-50 border-2 border-yellow-200' : 'bg-gray-50 border-2 border-gray-200'
                      }`}
                    >
                      <div className={`text-2xl mb-2 ${earned ? '' : 'opacity-30'}`}>
                        {badgeInfo.icon}
                      </div>
                      <div className={`text-xs font-medium ${earned ? 'text-yellow-800' : 'text-gray-500'}`}>
                        {badgeInfo.name}
                      </div>
                      <div className={`text-xs mt-1 ${earned ? 'text-yellow-600' : 'text-gray-400'}`}>
                        {badgeInfo.description}
                      </div>
                    </div>
                  );
                })}
              </div>
            </div>
          </div>
        )}

        {currentScreen === 'report' && (
          <div className="space-y-6">
            <div className="bg-white rounded-2xl p-6 shadow-sm">
              <h2 className="text-xl font-bold text-gray-800 mb-6 text-center">이번 주 리포트</h2>
              
              {(() => {
                const reportData = getReportData();
                return (
                  <div className="space-y-6">
                    <div className="grid grid-cols-2 gap-4">
                      <div className="text-center p-4 bg-purple-50 rounded-lg">
                        <div className="text-2xl font-bold text-purple-600">
                          {Math.floor(reportData.totalTime / 60)}시간
                        </div>
                        <div className="text-sm text-gray-600">총 드로잉 시간</div>
                      </div>
                      <div className="text-center p-4 bg-pink-50 rounded-lg">
                        <div className="text-2xl font-bold text-pink-600">
                          {reportData.completedDays}일
                        </div>
                        <div className="text-sm text-gray-600">완료한 날</div>
                      </div>
                    </div>
                    
                    <div className="text-center p-4 bg-yellow-50 rounded-lg">
                      <div className="text-2xl font-bold text-yellow-600">
                        {reportData.avgFocus.toFixed(1)}/5
                      </div>
                      <div className="text-sm text-gray-600">평균 집중도</div>
                    </div>
                    
                    <div className="p-4 bg-gray-50 rounded-lg">
                      <h4 className="text-lg font-semibold text-gray-800 mb-3">감정 분포</h4>
                      <div className="space-y-2">
                        {Object.entries(reportData.emotions).map(([emotionId, count]) => {
                          const emotion = emotionOptions.find(e => e.id === emotionId);
                          if (!emotion) return null;
                          
                          return (
                            <div key={emotionId} className="flex items-center justify-between">
                              <div className="flex items-center space-x-2">
                                <span className="text-lg">{emotion.emoji}</span>
                                <span className="text-sm text-gray-700">{emotion.label}</span>
                              </div>
                              <span className="text-sm font-semibold text-gray-800">{count}회</span>
                            </div>
                          );
                        })}
                      </div>
                    </div>
                  </div>
                );
              })()}
            </div>
          </div>
        )}

        {currentScreen === 'settings' && (
          <div className="space-y-6">
            <div className="bg-white rounded-2xl p-6 shadow-sm">
              <h2 className="text-xl font-bold text-gray-800 mb-6 text-center">설정</h2>
              
              <div className="space-y-4">
                <div className="p-4 bg-gray-50 rounded-lg">
                  <h3 className="text-lg font-semibold text-gray-800 mb-2">사용자 정보</h3>
                  <div className="flex items-center space-x-3">
                    <div className="text-3xl">🎨</div>
                    <div>
                      <p className="font-medium text-gray-800">{user?.nickname}</p>
                      <p className="text-sm text-gray-600">드로잉 러버</p>
                    </div>
                  </div>
                </div>

                <div className="p-4 bg-gray-50 rounded-lg">
                  <h3 className="text-lg font-semibold text-gray-800 mb-3">앱 정보</h3>
                  <div className="space-y-2">
                    <div className="flex justify-between items-center">
                      <span className="text-sm text-gray-600">버전</span>
                      <span className="text-sm font-medium text-gray-800">v{APP_VERSION}</span>
                    </div>
                    <div className="flex justify-between items-center">
                      <span className="text-sm text-gray-600">개발자</span>
                      <span className="text-sm font-medium text-gray-800">GRIDA Team</span>
                    </div>
                  </div>
                </div>

                <div className="p-4 bg-gray-50 rounded-lg">
                  <h3 className="text-lg font-semibold text-gray-800 mb-3">나의 기록</h3>
                  <div className="space-y-2">
                    <div className="flex justify-between items-center">
                      <span className="text-sm text-gray-600">완료 횟수</span>
                      <span className="text-sm font-medium text-purple-600">{Object.keys(getCalendarData()).length}회</span>
                    </div>
                    <div className="flex justify-between items-center">
                      <span className="text-sm text-gray-600">획득 뱃지</span>
                      <span className="text-sm font-medium text-purple-600">{badges.length}개</span>
                    </div>
                    <div className="flex justify-between items-center">
                      <span className="text-sm text-gray-600">활성 목표</span>
                      <span className="text-sm font-medium text-purple-600">{goalTrackers.length}개</span>
                    </div>
                  </div>
                </div>

                <button
                  onClick={() => setShowLogoutConfirm(true)}
                  className="w-full flex items-center justify-center space-x-2 py-3 px-4 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors"
                >
                  <LogOut size={20} />
                  <span>로그아웃</span>
                </button>
              </div>
            </div>
          </div>
        )}
      </div>

      <nav className="fixed bottom-0 left-0 right-0 bg-white border-t border-gray-200">
        <div className="max-w-md mx-auto flex">
          {[
            { id: 'home', icon: Home, label: '홈' },
            { id: 'goals', icon: Target, label: '목표' },
            { id: 'report', icon: BarChart3, label: '리포트' },
            { id: 'settings', icon: Settings, label: '설정' }
          ].map((item) => (
            <button
              key={item.id}
              onClick={() => setCurrentScreen(item.id)}
              className={`flex-1 flex flex-col items-center py-3 transition-colors ${
                currentScreen === item.id
                  ? 'text-purple-500'
                  : 'text-gray-400 hover:text-gray-600'
              }`}
            >
              <item.icon size={24} />
              <span className="text-xs mt-1">{item.label}</span>
            </button>
          ))}
        </div>
      </nav>

      {showCreateTracker && (
        <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
          <div className="bg-white rounded-2xl p-6 max-w-sm w-full">
            <h3 className="text-lg font-bold text-gray-800 mb-4 text-center">새 목표 만들기</h3>
            <form onSubmit={(e) => {
              e.preventDefault();
              const formData = new FormData(e.target);
              const name = formData.get('name');
              const target = parseInt(formData.get('target'));
              const description = formData.get('description');
              
              if (name && target && description) {
                createGoalTracker(name, target, description);
              }
            }}>
              <div className="space-y-4">
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">
                    목표 이름
                  </label>
                  <input
                    type="text"
                    name="name"
                    placeholder="예: 인물화 마스터"
                    className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-transparent"
                    required
                  />
                </div>
                
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">
                    목표 횟수
                  </label>
                  <input
                    type="number"
                    name="target"
                    min="1"
                    max="100"
                    defaultValue="20"
                    className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-transparent"
                    required
                  />
                </div>
                
                <div>
                  <label className="block text-sm font-medium text-gray-700 mb-2">
                    목표 설명
                  </label>
                  <textarea
                    name="description"
                    placeholder="예: 인물화 기초를 익히기 위한 연습"
                    rows="3"
                    className="w-full p-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-purple-500 focus:border-transparent resize-none"
                    required
                  />
                </div>
              </div>
              
              <div className="flex space-x-3 mt-6">
                <button
                  type="button"
                  onClick={() => setShowCreateTracker(false)}
                  className="flex-1 py-3 px-4 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors"
                >
                  취소
                </button>
                <button
                  type="submit"
                  className="flex-1 py-3 px-4 bg-purple-500 text-white rounded-lg hover:bg-purple-600 transition-colors"
                >
                  생성
                </button>
              </div>
            </form>
          </div>
        </div>
      )}

      {showLogoutConfirm && (
        <div className="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50 p-4">
          <div className="bg-white rounded-2xl p-6 max-w-sm w-full">
            <h3 className="text-lg font-bold text-gray-800 mb-4 text-center">로그아웃</h3>
            <p className="text-gray-600 text-center mb-6">
              정말로 로그아웃하시겠습니까?<br />
              모든 데이터가 삭제됩니다.
            </p>
            <div className="flex space-x-3">
              <button
                onClick={() => setShowLogoutConfirm(false)}
                className="flex-1 py-3 px-4 border border-gray-300 text-gray-700 rounded-lg hover:bg-gray-50 transition-colors"
              >
                취소
              </button>
              <button
                onClick={handleLogout}
                className="flex-1 py-3 px-4 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors"
              >
                로그아웃
              </button>
            </div>
          </div>
        </div>
      )}
    </div>
  );
};

export default GRIDA;
