/**
 * @license
 * SPDX-License-Identifier: Apache-2.0
 */

import React, { useState, useEffect, useMemo, useRef } from 'react';
import {
  Volume2,
  Shuffle,
  Eye,
  EyeOff,
  Check,
  Bookmark,
  ChevronLeft,
  ChevronRight,
  Search,
  RotateCcw,
  Download,
  CheckCircle2,
  Globe,
  Keyboard,
  CreditCard
} from 'lucide-react';
import {
  QUESTIONS,
  CHAPTERS,
  DAY_SCHEDULES,
  AppLanguage,
  StudyCardItem,
  sampleRandomIds,
  buildBidirectionalSessionCards
} from './data/questions';
import { UI_STRINGS } from './data/i18n';
import { speakEcuadorSpanish } from './utils/ecuadorTts';
import { downloadStandaloneChromeHtml } from './utils/exportStandaloneHtml';
import {
  QUESTION_KEYWORD_SPECS,
  TypingValidationResult,
  evaluateTypingSubmission
} from './utils/typingValidator';

type ActiveSection = 'study' | 'exam' | 'catalog';
type StudyMethod = 'card' | 'typing';
type StudyLayoutMode = 'cards_list' | 'single_focus';
type OriginFilter = 'ALL' | 'NEW' | 'REVIEW';

interface DailyReviewStore {
  [day: number]: number[];
}

interface TypingCardState {
  answer: string;
  verse: string;
  submitted: boolean;
  result: TypingValidationResult | null;
}

export default function App() {
  // Language selection state: null = initial Language Selection Gate screen
  const [language, setLanguage] = useState<AppLanguage | null>(null);

  // Navigation section
  const [activeSection, setActiveSection] = useState<ActiveSection>('study');

  // Study method: 1. 카드 ('card') | 2. 타이핑 ('typing')
  const [studyMethod, setStudyMethod] = useState<StudyMethod>('card');

  // Persisted curriculum & progress state
  const [selectedDay, setSelectedDay] = useState<number>(() => {
    const saved = localStorage.getItem('parabolas99_v2_selected_day');
    return saved ? Math.min(20, Math.max(1, Number(saved))) : 1;
  });

  const [completedDays, setCompletedDays] = useState<number[]>(() => {
    try {
      const saved = localStorage.getItem('parabolas99_v2_completed_days');
      return saved ? JSON.parse(saved) : [];
    } catch {
      return [];
    }
  });

  const [masteredIds, setMasteredIds] = useState<number[]>(() => {
    try {
      const saved = localStorage.getItem('parabolas99_v2_mastered_ids');
      return saved ? JSON.parse(saved) : [];
    } catch {
      return [];
    }
  });

  const [bookmarkedIds, setBookmarkedIds] = useState<number[]>(() => {
    try {
      const saved = localStorage.getItem('parabolas99_v2_bookmarked_ids');
      return saved ? JSON.parse(saved) : [];
    } catch {
      return [];
    }
  });

  const [dailyReviews, setDailyReviews] = useState<DailyReviewStore>(() => {
    try {
      const saved = localStorage.getItem('parabolas99_v2_daily_reviews');
      return saved ? JSON.parse(saved) : {};
    } catch {
      return {};
    }
  });

  // Study controls
  const [prioritizeWeakInRandom, setPrioritizeWeakInRandom] = useState<boolean>(false);
  const [studyLayout, setStudyLayout] = useState<StudyLayoutMode>('cards_list');
  const [originFilter, setOriginFilter] = useState<OriginFilter>('ALL');
  const [revealedUids, setRevealedUids] = useState<string[]>([]);
  const [revealAll, setRevealAll] = useState<boolean>(false);
  const [focusCardIndex, setFocusCardIndex] = useState<number>(0);

  // Typing study state per card uid
  const [typingEntries, setTypingEntries] = useState<Record<string, TypingCardState>>({});

  // Exam state (10 questions per day: Question -> Answer ONLY, 100% Typing)
  const [examIndex, setExamIndex] = useState<number>(0);
  const [examInput, setExamInput] = useState<string>('');
  const [examVerseInput, setExamVerseInput] = useState<string>('');
  const [examSubmitted, setExamSubmitted] = useState<boolean>(false);
  const [examValidation, setExamValidation] = useState<TypingValidationResult | null>(null);
  const [examResults, setExamResults] = useState<Record<string, 'correct' | 'review_again'>>({});
  const [examShuffleOrder, setExamShuffleOrder] = useState<boolean>(false);
  const [examShowResults, setExamShowResults] = useState<boolean>(false);
  const [examMistakeUids, setExamMistakeUids] = useState<string[] | null>(null);

  // Refs for seamless keyboard Enter -> Check -> Enter -> Next question flow
  const examAnswerInputRef = useRef<HTMLInputElement | null>(null);
  const studyAnswerInputRefs = useRef<Record<string, HTMLInputElement | null>>({});
  const lastSubmitTimeRef = useRef<number>(0);

  // Catalog filter state
  const [searchQuery, setSearchQuery] = useState<string>('');
  const [selectedChapter, setSelectedChapter] = useState<number>(0);
  const [catalogStatusFilter, setCatalogStatusFilter] = useState<
    'all' | 'bookmarked' | 'mastered' | 'unmastered'
  >('all');

  // Persist to localStorage
  useEffect(() => {
    localStorage.setItem('parabolas99_v2_selected_day', String(selectedDay));
  }, [selectedDay]);

  useEffect(() => {
    localStorage.setItem('parabolas99_v2_completed_days', JSON.stringify(completedDays));
  }, [completedDays]);

  useEffect(() => {
    localStorage.setItem('parabolas99_v2_mastered_ids', JSON.stringify(masteredIds));
  }, [masteredIds]);

  useEffect(() => {
    localStorage.setItem('parabolas99_v2_bookmarked_ids', JSON.stringify(bookmarkedIds));
  }, [bookmarkedIds]);

  useEffect(() => {
    localStorage.setItem('parabolas99_v2_daily_reviews', JSON.stringify(dailyReviews));
  }, [dailyReviews]);

  const currentSchedule = useMemo(
    () => DAY_SCHEDULES.find((d) => d.day === selectedDay) || DAY_SCHEDULES[0],
    [selectedDay]
  );

  // Ensure the selected day has its random review concepts generated
  useEffect(() => {
    if (
      currentSchedule.reviewConceptCount > 0 &&
      (!dailyReviews[selectedDay] ||
        dailyReviews[selectedDay].length !== currentSchedule.reviewConceptCount)
    ) {
      const weakPool = currentSchedule.learnedPoolIds.filter(
        (id) => bookmarkedIds.includes(id) || !masteredIds.includes(id)
      );
      const picked = sampleRandomIds(
        currentSchedule.learnedPoolIds,
        currentSchedule.reviewConceptCount,
        prioritizeWeakInRandom ? weakPool : []
      );
      setDailyReviews((prev) => ({ ...prev, [selectedDay]: picked }));
    }
  }, [
    selectedDay,
    currentSchedule,
    dailyReviews,
    prioritizeWeakInRandom,
    bookmarkedIds,
    masteredIds
  ]);

  const handleRerollReviews = () => {
    if (currentSchedule.reviewConceptCount === 0) return;
    const weakPool = currentSchedule.learnedPoolIds.filter(
      (id) => bookmarkedIds.includes(id) || !masteredIds.includes(id)
    );
    const picked = sampleRandomIds(
      currentSchedule.learnedPoolIds,
      currentSchedule.reviewConceptCount,
      prioritizeWeakInRandom ? weakPool : []
    );
    setDailyReviews((prev) => ({ ...prev, [selectedDay]: picked }));
    setFocusCardIndex(0);
    setExamIndex(0);
    setExamSubmitted(false);
    setExamValidation(null);
    setExamInput('');
    setExamVerseInput('');
    setExamShowResults(false);
    setExamMistakeUids(null);
    setExamResults({});
  };

  // All 10 Question -> Answer cards for the current day
  const allDayCards = useMemo(() => {
    const revConceptIds =
      currentSchedule.reviewConceptCount > 0 ? dailyReviews[selectedDay] || [] : [];
    return buildBidirectionalSessionCards(currentSchedule.newConceptIds, revConceptIds);
  }, [currentSchedule, dailyReviews, selectedDay]);

  // Filtered study cards
  const filteredStudyCards = useMemo(() => {
    return allDayCards.filter((card) => {
      if (originFilter !== 'ALL' && card.origin !== originFilter) return false;
      return true;
    });
  }, [allDayCards, originFilter]);

  // Exam items
  const examCards = useMemo(() => {
    if (!examShuffleOrder) return allDayCards;
    return [...allDayCards].sort((a, b) => {
      const hashA = (a.question.id * 37 + 11) % 101;
      const hashB = (b.question.id * 37 + 11) % 101;
      return hashA - hashB;
    });
  }, [allDayCards, examShuffleOrder]);

  const activeExamCards = useMemo(() => {
    if (examMistakeUids && examMistakeUids.length > 0) {
      return examCards.filter((c) => examMistakeUids.includes(c.uid));
    }
    return examCards;
  }, [examCards, examMistakeUids]);

  const currentExamCard = activeExamCards[examIndex] || activeExamCards[0];



  const toggleMastered = (id: number) => {
    setMasteredIds((prev) =>
      prev.includes(id) ? prev.filter((x) => x !== id) : [...prev, id]
    );
  };

  const toggleBookmark = (id: number) => {
    setBookmarkedIds((prev) =>
      prev.includes(id) ? prev.filter((x) => x !== id) : [...prev, id]
    );
  };

  const toggleRevealCard = (uid: string) => {
    setRevealedUids((prev) =>
      prev.includes(uid) ? prev.filter((x) => x !== uid) : [...prev, uid]
    );
  };

  const toggleCompleteDay = (day: number) => {
    setCompletedDays((prev) =>
      prev.includes(day) ? prev.filter((d) => d !== day) : [...prev, day]
    );
  };

  // Typing mode helpers
  const getTypingEntry = (uid: string): TypingCardState => {
    return (
      typingEntries[uid] || {
        answer: '',
        verse: '',
        submitted: false,
        result: null
      }
    );
  };

  const updateTypingAnswer = (uid: string, answer: string) => {
    setTypingEntries((prev) => {
      const existing = prev[uid] || { answer: '', verse: '', submitted: false, result: null };
      return {
        ...prev,
        [uid]: { ...existing, answer, submitted: false, result: null }
      };
    });
  };

  const updateTypingVerse = (uid: string, verse: string) => {
    setTypingEntries((prev) => {
      const existing = prev[uid] || { answer: '', verse: '', submitted: false, result: null };
      return {
        ...prev,
        [uid]: { ...existing, verse, submitted: false, result: null }
      };
    });
  };

  const submitTypingCard = (card: StudyCardItem) => {
    if (!language) return;
    lastSubmitTimeRef.current = Date.now();
    const entry = getTypingEntry(card.uid);
    const result = evaluateTypingSubmission(
      card.question,
      entry.answer,
      entry.verse,
      language
    );
    setTypingEntries((prev) => ({
      ...prev,
      [card.uid]: {
        ...entry,
        submitted: true,
        result
      }
    }));
    if (result.isCorrect && !masteredIds.includes(card.question.id)) {
      setMasteredIds((prev) => [...prev, card.question.id]);
    }
  };

  const resetTypingCard = (uid: string) => {
    setTypingEntries((prev) => ({
      ...prev,
      [uid]: { answer: '', verse: '', submitted: false, result: null }
    }));
    setTimeout(() => {
      studyAnswerInputRefs.current[uid]?.focus();
    }, 20);
  };

  const advanceStudyTypingCard = (currentUid: string) => {
    if (filteredStudyCards.length === 0) return;
    const currentIdx = filteredStudyCards.findIndex((c) => c.uid === currentUid);
    if (studyLayout === 'single_focus') {
      const nextIdx =
        currentIdx >= 0 && currentIdx < filteredStudyCards.length - 1
          ? currentIdx + 1
          : 0;
      setFocusCardIndex(nextIdx);
      const nextCard = filteredStudyCards[nextIdx];
      if (nextCard) {
        setTimeout(() => {
          studyAnswerInputRefs.current[nextCard.uid]?.focus();
        }, 30);
      }
    } else {
      const nextIdx =
        currentIdx >= 0 && currentIdx < filteredStudyCards.length - 1
          ? currentIdx + 1
          : 0;
      const nextCard = filteredStudyCards[nextIdx];
      if (nextCard) {
        const el = studyAnswerInputRefs.current[nextCard.uid];
        if (el) {
          el.scrollIntoView({ behavior: 'smooth', block: 'center' });
          el.focus();
        }
      }
    }
  };

  const submitCurrentExamCard = () => {
    if (!language || !currentExamCard) return;
    lastSubmitTimeRef.current = Date.now();
    const res = evaluateTypingSubmission(
      currentExamCard.question,
      examInput,
      examVerseInput,
      language
    );
    setExamValidation(res);
    setExamSubmitted(true);
    setExamResults((prev) => ({
      ...prev,
      [currentExamCard.uid]: res.isCorrect ? 'correct' : 'review_again'
    }));
    if (res.isCorrect && !masteredIds.includes(currentExamCard.question.id)) {
      setMasteredIds((prev) => [...prev, currentExamCard.question.id]);
    }
  };

  const advanceExamToNextOrFinish = () => {
    if (!language || !currentExamCard) return;
    if (!examSubmitted) {
      const res = evaluateTypingSubmission(
        currentExamCard.question,
        examInput,
        examVerseInput,
        language
      );
      setExamValidation(res);
      setExamSubmitted(true);
      setExamResults((prev) => ({
        ...prev,
        [currentExamCard.uid]: res.isCorrect ? 'correct' : 'review_again'
      }));
    }
    if (examIndex < activeExamCards.length - 1) {
      setExamIndex((i) => Math.min(activeExamCards.length - 1, i + 1));
      setExamSubmitted(false);
      setExamValidation(null);
      setExamInput('');
      setExamVerseInput('');
      setTimeout(() => {
        examAnswerInputRef.current?.focus();
      }, 30);
    } else {
      setExamShowResults(true);
    }
  };

  // Auto-focus exam answer input when exam question index changes
  useEffect(() => {
    if (activeSection === 'exam' && !examSubmitted && !examShowResults) {
      const timer = setTimeout(() => {
        examAnswerInputRef.current?.focus();
      }, 30);
      return () => clearTimeout(timer);
    }
  }, [activeSection, examIndex, examSubmitted, examShowResults]);

  // Global Enter listener when an exam question is already submitted so pressing Enter anywhere advances
  useEffect(() => {
    if (activeSection !== 'exam' || !examSubmitted || examShowResults) return;
    const handleWindowKeyDown = (e: KeyboardEvent) => {
      if (e.key === 'Enter') {
        if (Date.now() - lastSubmitTimeRef.current < 150) return;
        e.preventDefault();
        advanceExamToNextOrFinish();
      }
    };
    window.addEventListener('keydown', handleWindowKeyDown);
    return () => window.removeEventListener('keydown', handleWindowKeyDown);
  }, [
    activeSection,
    examSubmitted,
    examShowResults,
    examIndex,
    activeExamCards.length,
    currentExamCard,
    examInput,
    examVerseInput,
    language
  ]);

  // Filtered catalog questions
  const filteredCatalog = useMemo(() => {
    if (!language) return [];
    const isKo = language === 'ko';
    return QUESTIONS.filter((q) => {
      if (selectedChapter !== 0 && q.chapterId !== selectedChapter) return false;
      if (catalogStatusFilter === 'bookmarked' && !bookmarkedIds.includes(q.id))
        return false;
      if (catalogStatusFilter === 'mastered' && !masteredIds.includes(q.id))
        return false;
      if (catalogStatusFilter === 'unmastered' && masteredIds.includes(q.id))
        return false;
      if (searchQuery.trim() !== '') {
        const kw = searchQuery.toLowerCase();
        if (isKo) {
          return (
            q.code.toLowerCase().includes(kw) ||
            q.termKo.toLowerCase().includes(kw) ||
            q.questionKo.toLowerCase().includes(kw) ||
            q.answerKo.toLowerCase().includes(kw) ||
            q.verseKo.toLowerCase().includes(kw)
          );
        } else {
          return (
            q.code.toLowerCase().includes(kw) ||
            q.termEs.toLowerCase().includes(kw) ||
            q.questionEs.toLowerCase().includes(kw) ||
            q.answerEs.toLowerCase().includes(kw) ||
            q.verseEs.toLowerCase().includes(kw)
          );
        }
      }
      return true;
    });
  }, [
    language,
    selectedChapter,
    catalogStatusFilter,
    bookmarkedIds,
    masteredIds,
    searchQuery
  ]);

  // ============================================================================
  // STEP 1: MINIMAL INITIAL LANGUAGE SELECTION GATE SCREEN
  // ============================================================================
  if (!language) {
    return (
      <div className="min-h-screen bg-[#F8FAFC] text-[#0F172A] flex flex-col justify-between p-6">
        <div className="flex-1 flex items-center justify-center">
          <div className="w-full max-w-xl bg-white border border-slate-200 rounded-2xl p-8 sm:p-10 text-center space-y-6">
            <div className="space-y-1.5">
              <div className="text-xs font-mono-tabular text-slate-400">
                99 Q&A · 10 DAILY
              </div>
              <h1 className="font-display text-3xl font-bold text-slate-900 tracking-tight">
                Parábolas 99
              </h1>
              <p className="text-xs text-slate-500">
                언어 선택 · Seleccione idioma
              </p>
            </div>

            <div className="grid grid-cols-1 sm:grid-cols-2 gap-4 pt-2">
              <button
                type="button"
                onClick={() => setLanguage('ko')}
                className="group p-6 rounded-xl border border-slate-200 bg-white hover:border-[#0284C7] hover:bg-[#0284C7]/[0.03] transition-all text-left flex flex-col justify-between space-y-4 cursor-pointer"
              >
                <div className="space-y-1">
                  <div className="font-display text-2xl font-bold text-slate-900">
                    한국어
                  </div>
                  <p className="text-xs text-slate-500">
                    한국어 전용 · 1. 카드 / 2. 타이핑
                  </p>
                </div>
                <div className="pt-3 border-t border-slate-100 flex items-center justify-between text-xs font-semibold text-[#0284C7]">
                  <span>시작</span>
                  <ChevronRight className="w-4 h-4 group-hover:translate-x-0.5 transition-transform" />
                </div>
              </button>

              <button
                type="button"
                onClick={() => setLanguage('es')}
                className="group p-6 rounded-xl border border-slate-200 bg-white hover:border-[#0284C7] hover:bg-[#0284C7]/[0.03] transition-all text-left flex flex-col justify-between space-y-4 cursor-pointer"
              >
                <div className="space-y-1">
                  <div className="font-display text-2xl font-bold text-slate-900">
                    Español
                  </div>
                  <p className="text-xs text-slate-500">
                    Solo español · Audio Ecuador
                  </p>
                </div>
                <div className="pt-3 border-t border-slate-100 flex items-center justify-between text-xs font-semibold text-[#0284C7]">
                  <span>Iniciar</span>
                  <ChevronRight className="w-4 h-4 group-hover:translate-x-0.5 transition-transform" />
                </div>
              </button>
            </div>
          </div>
        </div>
      </div>
    );
  }

  // ============================================================================
  // STEP 2: MINIMAL SINGLE-LANGUAGE WORKSPACE
  // ============================================================================
  const isKo = language === 'ko';
  const t = UI_STRINGS[language];
  const currentFocusCard =
    filteredStudyCards[
      Math.min(focusCardIndex, Math.max(0, filteredStudyCards.length - 1))
    ];

  const renderCardPrompt = (card: StudyCardItem) => {
    return isKo ? card.question.questionKo : card.question.questionEs;
  };

  const renderCardAnswerMain = (card: StudyCardItem) => {
    return isKo ? card.question.answerKo : card.question.answerEs;
  };

  const renderCardVerse = (card: StudyCardItem) => {
    return isKo ? card.question.verseKo : card.question.verseEs;
  };

  const isSingleShortQuestion = (questionId: number) => {
    const spec = QUESTION_KEYWORD_SPECS[questionId];
    if (!spec) return false;
    const slots = isKo ? spec.koSlots : spec.esSlots;
    return spec.isShort && slots.length === 1;
  };

  // Renders the interactive Typing Box for a given study card
  const renderTypingStudyBox = (card: StudyCardItem) => {
    const q = card.question;
    const entry = getTypingEntry(card.uid);
    const shortMode = isSingleShortQuestion(q.id);
    const verseStr = renderCardVerse(card).trim();
    const hasVerse = verseStr !== '-' && verseStr.length > 0;
    const verseCount = hasVerse ? verseStr.split(',').filter(Boolean).length : 0;
    const isNextReady = Boolean(entry.submitted && entry.result?.isCorrect);

    const handleStudyInputKeyDown = (e: React.KeyboardEvent<HTMLInputElement>) => {
      if (e.key === 'Enter') {
        e.preventDefault();
        e.stopPropagation();
        if (isNextReady) {
          if (Date.now() - lastSubmitTimeRef.current >= 150) {
            advanceStudyTypingCard(card.uid);
          }
        } else {
          submitTypingCard(card);
        }
      }
    };

    return (
      <div className="space-y-3 pt-2 border-t border-slate-100">
        {/* Rule Indicator */}
        <div className="flex flex-wrap items-center justify-between gap-2 text-xs">
          <span
            className={`font-semibold ${
              shortMode ? 'text-[#0284C7]' : 'text-slate-600'
            }`}
          >
            {shortMode ? t.typingBadgeShort : t.typingBadgeKeywords}
          </span>
          {hasVerse && (
            <span className="font-mono-tabular text-slate-400">
              {t.labelKeyVerse} ({verseCount})
            </span>
          )}
        </div>

        {/* Meaning Answer Input + Evidence Verse Input */}
        <div className="grid grid-cols-1 sm:grid-cols-12 gap-2.5">
          <div className={hasVerse ? 'sm:col-span-7' : 'sm:col-span-12'}>
            <input
              ref={(el) => {
                studyAnswerInputRefs.current[card.uid] = el;
              }}
              type="text"
              value={entry.answer}
              onChange={(e) => updateTypingAnswer(card.uid, e.target.value)}
              onKeyDown={handleStudyInputKeyDown}
              placeholder={
                shortMode
                  ? t.typingAnswerPlaceholderShort
                  : t.typingAnswerPlaceholderKeywords
              }
              className="w-full px-3.5 py-2.5 text-sm bg-white border border-slate-300 rounded-lg focus:outline-none focus:border-[#0284C7]"
            />
          </div>

          {hasVerse && (
            <div className="sm:col-span-5">
              <input
                type="text"
                value={entry.verse}
                onChange={(e) => updateTypingVerse(card.uid, e.target.value)}
                onKeyDown={handleStudyInputKeyDown}
                placeholder={t.typingVersePlaceholder}
                className="w-full px-3.5 py-2.5 text-sm font-mono-tabular bg-white border border-slate-300 rounded-lg focus:outline-none focus:border-[#0284C7]"
              />
            </div>
          )}
        </div>

        {/* Action & Next Question Button Bar */}
        <div className="flex flex-wrap items-center justify-end gap-2">
          {!entry.submitted ? (
            <button
              type="button"
              onClick={() => submitTypingCard(card)}
              className="px-4 py-2 text-xs font-semibold text-white bg-[#0284C7] rounded-lg hover:bg-[#0369A1] transition-colors whitespace-nowrap shrink-0"
            >
              {t.btnCheckTyping}
            </button>
          ) : (
            <button
              type="button"
              onClick={() => resetTypingCard(card.uid)}
              className="px-3.5 py-2 text-xs font-semibold border border-slate-200 bg-white rounded-lg text-slate-700 hover:bg-slate-50 whitespace-nowrap shrink-0"
            >
              {t.btnRetryTyping}
            </button>
          )}

          <button
            type="button"
            disabled={!isNextReady}
            onClick={() => advanceStudyTypingCard(card.uid)}
            className={`px-4 py-2 text-xs font-bold rounded-lg transition-all flex items-center gap-1 whitespace-nowrap shrink-0 ${
              isNextReady
                ? 'bg-[#059669] text-white hover:bg-[#047857] ring-2 ring-[#059669]/30 shadow-xs cursor-pointer'
                : 'bg-slate-100 text-slate-400 border border-slate-200 cursor-not-allowed'
            }`}
          >
            <span>
              {isNextReady
                ? isKo
                  ? `${t.btnNextCard} (엔터 ↵)`
                  : `${t.btnNextCard} (Intro ↵)`
                : t.btnNextCard}
            </span>
            <ChevronRight className="w-3.5 h-3.5" />
          </button>
        </div>

        {/* Validation Result Box */}
        {entry.submitted && (
          <div
            className={`rounded-xl border p-4 space-y-2.5 ${
              entry.result?.isCorrect
                ? 'bg-[#059669]/[0.05] border-[#059669]/40'
                : 'bg-red-50/60 border-red-200'
            }`}
          >
            <div className="flex flex-wrap items-center justify-between gap-2">
              <span
                className={`text-xs font-bold ${
                  entry.result?.isCorrect ? 'text-[#059669]' : 'text-red-600'
                }`}
              >
                {entry.result?.isCorrect
                  ? t.typingResultCorrect
                  : t.typingResultWrong}
              </span>
              {isNextReady && (
                <button
                  type="button"
                  onClick={() => advanceStudyTypingCard(card.uid)}
                  className="px-3 py-1 text-xs font-bold text-white bg-[#059669] hover:bg-[#047857] rounded-md flex items-center gap-1 whitespace-nowrap shrink-0"
                >
                  <span>
                    {isKo
                      ? `${t.btnNextCard} (엔터 ↵)`
                      : `${t.btnNextCard} (Intro ↵)`}
                  </span>
                  <ChevronRight className="w-3.5 h-3.5" />
                </button>
              )}
            </div>

            {entry.result && !entry.result.isCorrect && (
              <div className="text-xs space-y-1 text-slate-700">
                {entry.result.missingKeywords.length > 0 && (
                  <div>
                    <span className="font-semibold text-red-600 mr-1.5">
                      {t.typingMissingKeywords}
                    </span>
                    <span className="font-mono-tabular">
                      {entry.result.missingKeywords.join(', ')}
                    </span>
                  </div>
                )}
                {entry.result.missingVerses.length > 0 && (
                  <div>
                    <span className="font-semibold text-red-600 mr-1.5">
                      {t.typingMissingVerses}
                    </span>
                    <span className="font-mono-tabular">
                      {entry.result.missingVerses.join(', ')}
                    </span>
                  </div>
                )}
              </div>
            )}

            <div className="pt-2 border-t border-slate-200/80 space-y-1">
              <div className="text-xl font-bold text-[#0284C7]">
                {renderCardAnswerMain(card)}
              </div>
              <div className="text-xl font-bold text-[#0284C7] font-mono-tabular">
                {t.labelKeyVerse}: {renderCardVerse(card)}
              </div>
            </div>
          </div>
        )}
      </div>
    );
  };

  return (
    <div className="min-h-screen flex flex-col bg-[#F8FAFC] text-[#0F172A]">
      {/* Top Bar Contract: 3 zones separated by gap-8 */}
      <header className="sticky top-0 z-30 bg-white border-b border-slate-200 px-6 py-3.5 flex items-center justify-between gap-8">
        <a
          href="#study"
          onClick={(e) => {
            e.preventDefault();
            setActiveSection('study');
          }}
          className="font-display text-lg font-bold tracking-tight text-slate-900 whitespace-nowrap shrink-0"
        >
          {t.brandTitle}
        </a>

        <nav className="flex items-center gap-6 text-sm font-medium text-slate-600 overflow-x-auto">
          <button
            type="button"
            onClick={() => setActiveSection('study')}
            className={`hover:text-slate-900 transition-colors whitespace-nowrap shrink-0 pb-0.5 border-b-2 ${
              activeSection === 'study'
                ? 'border-[#0284C7] text-slate-900 font-semibold'
                : 'border-transparent'
            }`}
          >
            {t.navStudy}
          </button>
          <button
            type="button"
            onClick={() => {
              setActiveSection('exam');
              setExamIndex(0);
              setExamSubmitted(false);
              setExamValidation(null);
              setExamInput('');
              setExamVerseInput('');
              setExamShowResults(false);
            }}
            className={`hover:text-slate-900 transition-colors whitespace-nowrap shrink-0 pb-0.5 border-b-2 ${
              activeSection === 'exam'
                ? 'border-[#0284C7] text-slate-900 font-semibold'
                : 'border-transparent'
            }`}
          >
            {t.navExam}
          </button>
          <button
            type="button"
            onClick={() => setActiveSection('catalog')}
            className={`hover:text-slate-900 transition-colors whitespace-nowrap shrink-0 pb-0.5 border-b-2 ${
              activeSection === 'catalog'
                ? 'border-[#0284C7] text-slate-900 font-semibold'
                : 'border-transparent'
            }`}
          >
            {t.navCatalog}
          </button>
          <button
            type="button"
            onClick={() => setLanguage(null)}
            className="hover:text-[#0284C7] transition-colors whitespace-nowrap shrink-0 pb-0.5 border-b-2 border-transparent flex items-center gap-1"
          >
            <Globe className="w-3.5 h-3.5" />
            <span>{t.navChangeLanguage}</span>
          </button>
        </nav>

        <div className="flex items-center shrink-0">
          <button
            type="button"
            onClick={downloadStandaloneChromeHtml}
            className="px-3.5 py-1.5 text-xs font-semibold text-white bg-[#0284C7] rounded-lg hover:bg-[#0369A1] transition-colors flex items-center gap-1.5 whitespace-nowrap shrink-0"
          >
            <Download className="w-3.5 h-3.5" />
            <span>{t.btnDownloadHtml}</span>
          </button>
        </div>
      </header>

      {/* Main Content Container */}
      <main className="flex-1 w-full max-w-[1280px] mx-auto px-6 py-6">
        {/* Minimal Summary Strip */}
        <section className="mb-6 pb-5 border-b border-slate-200 flex flex-col sm:flex-row sm:items-end justify-between gap-4">
          <div>
            <div className="text-xs text-slate-400 mb-1 font-mono-tabular">
              {t.kickerSummary}
            </div>
            <h1 className="font-display text-2xl font-bold text-slate-900 tracking-tight">
              {isKo ? currentSchedule.titleKo : currentSchedule.titleEs}
            </h1>
          </div>

          <div className="flex items-center gap-6 text-left">
            <div>
              <div className="text-xs text-slate-400">{t.statTodayComposition}</div>
              <div className="text-base font-bold text-[#0284C7] font-mono-tabular">
                {currentSchedule.newConceptIds.length} + {currentSchedule.reviewConceptCount}
              </div>
            </div>
            <div>
              <div className="text-xs text-slate-400">{t.statMasteredConcepts}</div>
              <div className="text-base font-bold text-[#059669] font-mono-tabular">
                {masteredIds.length}/99
              </div>
            </div>
            <div>
              <div className="text-xs text-slate-400">{t.statBookmarkedConcepts}</div>
              <div className="text-base font-bold text-[#D97706] font-mono-tabular">
                {bookmarkedIds.length}
              </div>
            </div>
          </div>
        </section>

        {/* Two-Zone Layout */}
        <div className="grid grid-cols-1 lg:grid-cols-12 gap-6 items-start">
          {/* LEFT / MAIN STAGE (8 cols) */}
          <div className="lg:col-span-8 space-y-4">
            {/* ============================================================== */}
            {/* SECTION 1: DAILY 10-QUESTION STUDY (1. 카드 / 2. 타이핑)        */}
            {/* ============================================================== */}
            {activeSection === 'study' && (
              <>
                {/* Study Method Switcher + Filter Toolbar */}
                <div className="bg-white border border-slate-200 rounded-xl p-3 flex flex-wrap items-center justify-between gap-2">
                  <div className="flex flex-wrap items-center gap-2">
                    {/* 2 Study Methods: 1. 카드 | 2. 타이핑 */}
                    <div className="flex items-center gap-1 p-1 bg-slate-900/5 border border-slate-200 rounded-lg">
                      <button
                        type="button"
                        onClick={() => setStudyMethod('card')}
                        className={`px-3.5 py-1.5 text-xs font-bold rounded-md transition-colors flex items-center gap-1.5 whitespace-nowrap shrink-0 ${
                          studyMethod === 'card'
                            ? 'bg-[#0284C7] text-white'
                            : 'text-slate-700 hover:text-slate-900'
                        }`}
                      >
                        <CreditCard className="w-3.5 h-3.5" />
                        <span>{t.studyMethodCard}</span>
                      </button>
                      <button
                        type="button"
                        onClick={() => setStudyMethod('typing')}
                        className={`px-3.5 py-1.5 text-xs font-bold rounded-md transition-colors flex items-center gap-1.5 whitespace-nowrap shrink-0 ${
                          studyMethod === 'typing'
                            ? 'bg-[#0284C7] text-white'
                            : 'text-slate-700 hover:text-slate-900'
                        }`}
                      >
                        <Keyboard className="w-3.5 h-3.5" />
                        <span>{t.studyMethodTyping}</span>
                      </button>
                    </div>

                    {/* All / New / Review filter */}
                    <div className="flex items-center gap-1 p-1 bg-slate-100 rounded-lg">
                      <button
                        type="button"
                        onClick={() => {
                          setOriginFilter('ALL');
                          setFocusCardIndex(0);
                        }}
                        className={`px-2.5 py-1 text-xs font-medium rounded-md transition-colors whitespace-nowrap shrink-0 ${
                          originFilter === 'ALL'
                            ? 'bg-white text-slate-900 shadow-xs'
                            : 'text-slate-600 hover:text-slate-900'
                        }`}
                      >
                        {t.filterAll10}
                      </button>
                      {currentSchedule.newConceptIds.length > 0 && (
                        <button
                          type="button"
                          onClick={() => {
                            setOriginFilter('NEW');
                            setFocusCardIndex(0);
                          }}
                          className={`px-2.5 py-1 text-xs font-medium rounded-md transition-colors whitespace-nowrap shrink-0 ${
                            originFilter === 'NEW'
                              ? 'bg-white text-slate-900 shadow-xs'
                              : 'text-slate-600 hover:text-slate-900'
                          }`}
                        >
                          {t.filterNew} ({currentSchedule.newConceptIds.length})
                        </button>
                      )}
                      {currentSchedule.reviewConceptCount > 0 && (
                        <button
                          type="button"
                          onClick={() => {
                            setOriginFilter('REVIEW');
                            setFocusCardIndex(0);
                          }}
                          className={`px-2.5 py-1 text-xs font-medium rounded-md transition-colors whitespace-nowrap shrink-0 ${
                            originFilter === 'REVIEW'
                              ? 'bg-white text-slate-900 shadow-xs'
                              : 'text-slate-600 hover:text-slate-900'
                          }`}
                        >
                          {t.filterReview} ({currentSchedule.reviewConceptCount})
                        </button>
                      )}
                    </div>
                  </div>

                  <div className="flex items-center gap-2">
                    <button
                      type="button"
                      onClick={() =>
                        setStudyLayout((prev) =>
                          prev === 'cards_list' ? 'single_focus' : 'cards_list'
                        )
                      }
                      className="px-3 py-1 text-xs font-medium border border-slate-200 rounded-lg text-slate-700 hover:bg-slate-50 transition-colors whitespace-nowrap shrink-0"
                    >
                      {studyLayout === 'cards_list' ? t.layoutList : t.layoutFocus}
                    </button>

                    {studyMethod === 'card' && (
                      <button
                        type="button"
                        onClick={() => {
                          setRevealAll((prev) => !prev);
                          setRevealedUids([]);
                        }}
                        className="px-3 py-1 text-xs font-medium border border-slate-200 rounded-lg text-slate-700 hover:bg-slate-50 transition-colors flex items-center gap-1.5 whitespace-nowrap shrink-0"
                      >
                        {revealAll ? (
                          <EyeOff className="w-3.5 h-3.5" />
                        ) : (
                          <Eye className="w-3.5 h-3.5" />
                        )}
                        <span>
                          {revealAll ? t.btnHideAllAnswers : t.btnShowAllAnswers}
                        </span>
                      </button>
                    )}
                  </div>
                </div>

                {/* Single Focus Card Mode */}
                {studyLayout === 'single_focus' && currentFocusCard ? (
                  <div className="bg-white border border-slate-200 rounded-xl p-6 space-y-5">
                    <div className="flex items-center justify-between gap-4 border-b border-slate-100 pb-3">
                      <div className="text-xs text-slate-500 font-mono-tabular">
                        <span className="font-bold text-slate-900">
                          {currentFocusCard.question.code}
                        </span>
                        <span className="mx-2">·</span>
                        <span
                          className={
                            currentFocusCard.origin === 'NEW'
                              ? 'text-[#0284C7] font-semibold'
                              : 'text-[#D97706] font-semibold'
                          }
                        >
                          {currentFocusCard.origin === 'NEW'
                            ? t.tagNewConcept
                            : t.tagReviewConcept}
                        </span>
                        <span className="mx-2">·</span>
                        <span>
                          {isKo
                            ? currentFocusCard.question.chapterTitleKo
                            : currentFocusCard.question.chapterTitleEs}
                        </span>
                      </div>
                      <div className="text-xs font-mono-tabular text-slate-400">
                        {focusCardIndex + 1} / {filteredStudyCards.length}
                      </div>
                    </div>

                    {/* Prompt Area */}
                    <div className="space-y-3 py-2">
                      <h2 className="text-xl font-bold text-slate-900 leading-snug">
                        {renderCardPrompt(currentFocusCard)}
                      </h2>

                      {!isKo && (
                        <div className="flex flex-wrap items-center gap-2">
                          <button
                            type="button"
                            onClick={() =>
                              speakEcuadorSpanish(currentFocusCard.question.questionEs)
                            }
                            className="px-2.5 py-1 text-xs font-medium border border-slate-200 rounded-lg text-slate-700 hover:bg-slate-50 flex items-center gap-1 whitespace-nowrap shrink-0"
                          >
                            <Volume2 className="w-3.5 h-3.5 text-[#0284C7]" />
                            <span>{t.btnListenPromptEc}</span>
                          </button>
                          <button
                            type="button"
                            onClick={() =>
                              speakEcuadorSpanish(currentFocusCard.question.answerEs)
                            }
                            className="px-2.5 py-1 text-xs font-medium border border-slate-200 rounded-lg text-slate-700 hover:bg-slate-50 flex items-center gap-1 whitespace-nowrap shrink-0"
                          >
                            <Volume2 className="w-3.5 h-3.5 text-[#059669]" />
                            <span>{t.btnListenAnswerEc}</span>
                          </button>
                        </div>
                      )}
                    </div>

                    {/* Method 1: Card Reveal OR Method 2: Typing Input */}
                    {studyMethod === 'card' ? (
                      (() => {
                        const isShown =
                          revealAll || revealedUids.includes(currentFocusCard.uid);
                        return (
                          <div className="border-t border-slate-100 pt-4">
                            {isShown ? (
                              <div
                                onClick={() => toggleRevealCard(currentFocusCard.uid)}
                                className="bg-slate-50 border border-slate-200 rounded-xl p-5 space-y-2 cursor-pointer"
                              >
                                <div className="text-xl font-bold text-[#0284C7]">
                                  {renderCardAnswerMain(currentFocusCard)}
                                </div>
                                <div className="text-xl font-bold text-[#0284C7] font-mono-tabular">
                                  {t.labelKeyVerse}: {renderCardVerse(currentFocusCard)}
                                </div>
                              </div>
                            ) : (
                              <button
                                type="button"
                                onClick={() => toggleRevealCard(currentFocusCard.uid)}
                                className="w-full py-8 bg-slate-50 hover:bg-slate-100/80 border border-dashed border-slate-300 rounded-xl text-sm font-medium text-slate-500 transition-colors"
                              >
                                {t.clickToRevealBox}
                              </button>
                            )}
                          </div>
                        );
                      })()
                    ) : (
                      renderTypingStudyBox(currentFocusCard)
                    )}

                    {/* Card Navigation & Self Check */}
                    <div className="flex flex-wrap items-center justify-between gap-3 pt-3 border-t border-slate-100">
                      <div className="flex items-center gap-2">
                        <button
                          type="button"
                          disabled={focusCardIndex === 0}
                          onClick={() => setFocusCardIndex((i) => Math.max(0, i - 1))}
                          className="px-3 py-1.5 text-xs font-semibold border border-slate-200 rounded-lg text-slate-700 hover:bg-slate-50 disabled:opacity-40 flex items-center gap-1 whitespace-nowrap shrink-0"
                        >
                          <ChevronLeft className="w-4 h-4" />
                          <span>{t.btnPrevCard}</span>
                        </button>
                        <button
                          type="button"
                          disabled={focusCardIndex >= filteredStudyCards.length - 1}
                          onClick={() =>
                            setFocusCardIndex((i) =>
                              Math.min(filteredStudyCards.length - 1, i + 1)
                            )
                          }
                          className="px-3 py-1.5 text-xs font-semibold border border-slate-200 rounded-lg text-slate-700 hover:bg-slate-50 disabled:opacity-40 flex items-center gap-1 whitespace-nowrap shrink-0"
                        >
                          <span>{t.btnNextCard}</span>
                          <ChevronRight className="w-4 h-4" />
                        </button>
                      </div>

                      <div className="flex items-center gap-2">
                        <button
                          type="button"
                          onClick={() => toggleBookmark(currentFocusCard.question.id)}
                          className={`px-3 py-1.5 text-xs font-semibold rounded-lg border transition-colors flex items-center gap-1 whitespace-nowrap shrink-0 ${
                            bookmarkedIds.includes(currentFocusCard.question.id)
                              ? 'border-[#D97706] bg-[#D97706]/10 text-[#D97706]'
                              : 'border-slate-200 text-slate-600 hover:bg-slate-50'
                          }`}
                        >
                          <Bookmark className="w-3.5 h-3.5" />
                          <span>{t.btnBookmark}</span>
                        </button>
                        <button
                          type="button"
                          onClick={() => toggleMastered(currentFocusCard.question.id)}
                          className={`px-3.5 py-1.5 text-xs font-semibold rounded-lg border transition-colors flex items-center gap-1 whitespace-nowrap shrink-0 ${
                            masteredIds.includes(currentFocusCard.question.id)
                              ? 'border-[#059669] bg-[#059669] text-white'
                              : 'border-slate-200 text-slate-700 hover:bg-slate-50'
                          }`}
                        >
                          <Check className="w-3.5 h-3.5" />
                          <span>
                            {masteredIds.includes(currentFocusCard.question.id)
                              ? t.btnMastered
                              : t.btnMarkMastered}
                          </span>
                        </button>
                      </div>
                    </div>
                  </div>
                ) : (
                  /* All 10 Cards List Mode */
                  <div className="space-y-3">
                    {filteredStudyCards.map((card, idx) => {
                      const q = card.question;
                      const isShown = revealAll || revealedUids.includes(card.uid);
                      const isMastered = masteredIds.includes(q.id);
                      const isBookmarked = bookmarkedIds.includes(q.id);

                      return (
                        <article
                          key={card.uid}
                          className="bg-white border border-slate-200 rounded-xl p-5 transition-colors"
                        >
                          {/* Unboxed Metadata Row */}
                          <div className="flex flex-wrap items-center justify-between gap-2 mb-2">
                            <div className="flex flex-wrap items-center gap-2 text-xs text-slate-400 font-mono-tabular">
                              <span className="font-bold text-slate-900">
                                {String(idx + 1).padStart(2, '0')}. {q.code}
                              </span>
                              <span aria-hidden="true">·</span>
                              <span
                                className={
                                  card.origin === 'NEW'
                                    ? 'text-[#0284C7] font-semibold'
                                    : 'text-[#D97706] font-semibold'
                                }
                              >
                                {card.origin === 'NEW'
                                  ? t.tagNewConcept
                                  : t.tagReviewConcept}
                              </span>
                              <span aria-hidden="true">·</span>
                              <span>
                                {isKo ? q.chapterTitleKo : q.chapterTitleEs}
                              </span>
                            </div>

                            <div className="flex items-center gap-1.5">
                              {!isKo && (
                                <>
                                  <button
                                    type="button"
                                    onClick={() => speakEcuadorSpanish(q.questionEs)}
                                    className="px-2 py-1 text-xs font-medium text-slate-600 hover:text-slate-900 border border-slate-200 rounded-md hover:bg-slate-50 flex items-center gap-1 whitespace-nowrap shrink-0"
                                  >
                                    <Volume2 className="w-3.5 h-3.5 text-[#0284C7]" />
                                    <span>{t.btnListenPromptEc}</span>
                                  </button>
                                  <button
                                    type="button"
                                    onClick={() => speakEcuadorSpanish(q.answerEs)}
                                    className="px-2 py-1 text-xs font-medium text-slate-600 hover:text-slate-900 border border-slate-200 rounded-md hover:bg-slate-50 flex items-center gap-1 whitespace-nowrap shrink-0"
                                  >
                                    <Volume2 className="w-3.5 h-3.5 text-[#059669]" />
                                    <span>{t.btnListenAnswerEc}</span>
                                  </button>
                                </>
                              )}

                              <button
                                type="button"
                                onClick={() => toggleBookmark(q.id)}
                                className={`px-2 py-1 text-xs font-medium border rounded-md transition-colors flex items-center gap-1 whitespace-nowrap shrink-0 ${
                                  isBookmarked
                                    ? 'border-[#D97706] bg-[#D97706]/10 text-[#D97706]'
                                    : 'border-slate-200 text-slate-500 hover:text-slate-900'
                                }`}
                              >
                                <Bookmark className="w-3.5 h-3.5" />
                                <span>{t.btnBookmark}</span>
                              </button>
                              <button
                                type="button"
                                onClick={() => toggleMastered(q.id)}
                                className={`px-2 py-1 text-xs font-semibold border rounded-md transition-colors flex items-center gap-1 whitespace-nowrap shrink-0 ${
                                  isMastered
                                    ? 'border-[#059669] bg-[#059669] text-white'
                                    : 'border-slate-200 text-slate-600 hover:text-slate-900'
                                }`}
                              >
                                <Check className="w-3.5 h-3.5" />
                                <span>
                                  {isMastered ? t.btnMastered : t.btnMarkMastered}
                                </span>
                              </button>
                            </div>
                          </div>

                          {/* Question Text */}
                          <h3 className="text-lg font-bold text-slate-900 leading-snug mb-3">
                            {renderCardPrompt(card)}
                          </h3>

                          {/* Study Mode 1: Card OR Study Mode 2: Typing */}
                          {studyMethod === 'card' ? (
                            isShown ? (
                              <div
                                onClick={() => toggleRevealCard(card.uid)}
                                className="bg-slate-50 border border-slate-200 rounded-lg p-4 cursor-pointer hover:bg-slate-100/70 transition-colors space-y-1.5"
                              >
                                <div className="text-xl font-bold text-[#0284C7]">
                                  {renderCardAnswerMain(card)}
                                </div>
                                <div className="flex items-center justify-between pt-1.5 border-t border-slate-200/70">
                                  <span className="text-xl font-bold text-[#0284C7] font-mono-tabular">
                                    {t.labelKeyVerse}: {renderCardVerse(card)}
                                  </span>
                                  <span className="text-xs text-slate-400">
                                    {t.btnHideAnswer}
                                  </span>
                                </div>
                              </div>
                            ) : (
                              <button
                                type="button"
                                onClick={() => toggleRevealCard(card.uid)}
                                className="w-full text-left px-4 py-2.5 bg-slate-50 hover:bg-slate-100 border border-dashed border-slate-300 rounded-lg text-xs font-medium text-slate-500 flex items-center justify-between transition-colors"
                              >
                                <span>{t.clickToRevealBox}</span>
                                <span className="font-mono-tabular text-[#0284C7] font-semibold">
                                  {t.btnShowAnswer}
                                </span>
                              </button>
                            )
                          ) : (
                            renderTypingStudyBox(card)
                          )}
                        </article>
                      );
                    })}
                  </div>
                )}
              </>
            )}

            {/* ============================================================== */}
            {/* SECTION 2: 10-QUESTION EXAM MODE                               */}
            {/* ============================================================== */}
            {activeSection === 'exam' && currentExamCard && (
              <>
                <div className="bg-white border border-slate-200 rounded-xl p-6 space-y-5">
                  <div className="flex flex-wrap items-center justify-between gap-3 pb-3 border-b border-slate-200">
                    <div>
                      <div className="text-xs text-slate-400 font-mono-tabular mb-0.5">
                        <span>
                          {examIndex + 1} / {activeExamCards.length}
                        </span>
                        <span className="mx-2">·</span>
                        <span
                          className={
                            currentExamCard.origin === 'NEW'
                              ? 'text-[#0284C7] font-semibold'
                              : 'text-[#D97706] font-semibold'
                          }
                        >
                          {currentExamCard.origin === 'NEW'
                            ? t.tagNewConcept
                            : t.tagReviewConcept}
                        </span>
                      </div>
                      <h2 className="font-display text-lg font-bold text-slate-900">
                        {examMistakeUids
                          ? `${t.examTitle(selectedDay)} · ${t.examReviewMistakesTitle}`
                          : t.examTitle(selectedDay)}
                      </h2>
                    </div>

                    <div className="flex items-center gap-2">
                      <div className="flex items-center gap-1.5 px-3 py-1.5 bg-[#0284C7]/10 text-[#0284C7] rounded-lg text-xs font-bold font-mono-tabular">
                        <Keyboard className="w-3.5 h-3.5" />
                        <span>{t.examModeWritten}</span>
                      </div>

                      <button
                        type="button"
                        onClick={() => {
                          setExamShuffleOrder((prev) => !prev);
                          setExamIndex(0);
                          setExamSubmitted(false);
                          setExamValidation(null);
                          setExamInput('');
                          setExamVerseInput('');
                          setExamShowResults(false);
                        }}
                        className="px-3 py-1 text-xs font-medium border border-slate-200 rounded-lg text-slate-700 hover:bg-slate-50 whitespace-nowrap shrink-0"
                      >
                        {examShuffleOrder ? t.examOriginalOrder : t.examShuffleOrder}
                      </button>
                    </div>
                  </div>

                  {/* Exam Question Prompt */}
                  <div className="py-1 space-y-2">
                    <div className="flex items-center justify-between">
                      <span className="text-xs font-semibold text-slate-400">
                        {currentExamCard.question.code}
                      </span>
                      {!isKo && (
                        <button
                          type="button"
                          onClick={() =>
                            speakEcuadorSpanish(currentExamCard.question.questionEs)
                          }
                          className="text-xs font-medium text-[#0284C7] hover:underline flex items-center gap-1"
                        >
                          <Volume2 className="w-3.5 h-3.5" />
                          <span>{t.btnListenPromptEc}</span>
                        </button>
                      )}
                    </div>
                    <p className="text-xl font-bold text-slate-900 leading-snug">
                      {renderCardPrompt(currentExamCard)}
                    </p>
                  </div>

                  {/* 100% Typing Exam Mode */}
                  <div className="space-y-3">
                    <div className="text-xs font-semibold text-[#0284C7]">
                      {isSingleShortQuestion(currentExamCard.question.id)
                        ? t.typingBadgeShort
                        : t.typingBadgeKeywords}
                    </div>

                    <div className="grid grid-cols-1 sm:grid-cols-12 gap-2.5">
                      <div className="sm:col-span-7">
                        <input
                          ref={examAnswerInputRef}
                          type="text"
                          value={examInput}
                          onChange={(e) => {
                            setExamInput(e.target.value);
                            if (examSubmitted && !examValidation?.isCorrect) {
                              setExamSubmitted(false);
                              setExamValidation(null);
                            }
                          }}
                          onKeyDown={(e) => {
                            if (e.key === 'Enter') {
                              e.preventDefault();
                              e.stopPropagation();
                              if (!examSubmitted) {
                                submitCurrentExamCard();
                              } else if (Date.now() - lastSubmitTimeRef.current >= 150) {
                                advanceExamToNextOrFinish();
                              }
                            }
                          }}
                          placeholder={
                            isSingleShortQuestion(currentExamCard.question.id)
                              ? t.typingAnswerPlaceholderShort
                              : t.typingAnswerPlaceholderKeywords
                          }
                          className="w-full px-3.5 py-2.5 text-sm bg-white border border-slate-300 rounded-lg focus:outline-none focus:border-[#0284C7]"
                        />
                      </div>
                      <div className="sm:col-span-5">
                        <input
                          type="text"
                          value={examVerseInput}
                          onChange={(e) => {
                            setExamVerseInput(e.target.value);
                            if (examSubmitted && !examValidation?.isCorrect) {
                              setExamSubmitted(false);
                              setExamValidation(null);
                            }
                          }}
                          onKeyDown={(e) => {
                            if (e.key === 'Enter') {
                              e.preventDefault();
                              e.stopPropagation();
                              if (!examSubmitted) {
                                submitCurrentExamCard();
                              } else if (Date.now() - lastSubmitTimeRef.current >= 150) {
                                advanceExamToNextOrFinish();
                              }
                            }
                          }}
                          placeholder={
                            renderCardVerse(currentExamCard).trim() === '-'
                              ? t.typingVerseNone
                              : t.typingVersePlaceholder
                          }
                          disabled={renderCardVerse(currentExamCard).trim() === '-'}
                          className="w-full px-3.5 py-2.5 text-sm font-mono-tabular bg-white border border-slate-300 rounded-lg focus:outline-none focus:border-[#0284C7] disabled:bg-slate-100"
                        />
                      </div>
                    </div>

                    {/* Exam Action Row: Submit + Activated Next Button */}
                    <div className="flex flex-wrap items-center justify-between gap-2">
                      <div className="flex items-center gap-2">
                        {!examSubmitted ? (
                          <button
                            type="button"
                            onClick={submitCurrentExamCard}
                            className="px-4 py-2 text-xs font-semibold text-white bg-[#0284C7] rounded-lg hover:bg-[#0369A1] transition-colors whitespace-nowrap shrink-0"
                          >
                            {t.examSubmitAndCheck}
                          </button>
                        ) : (
                          <button
                            type="button"
                            onClick={() => {
                              setExamSubmitted(false);
                              setExamValidation(null);
                              setExamInput('');
                              setExamVerseInput('');
                              setTimeout(() => {
                                examAnswerInputRef.current?.focus();
                              }, 20);
                            }}
                            className="px-3.5 py-2 text-xs font-semibold border border-slate-200 bg-white rounded-lg text-slate-700 hover:bg-slate-50 whitespace-nowrap shrink-0"
                          >
                            {t.btnRetryTyping}
                          </button>
                        )}
                      </div>

                      <button
                        type="button"
                        disabled={!examSubmitted}
                        onClick={advanceExamToNextOrFinish}
                        className={`px-4 py-2 text-xs font-bold rounded-lg transition-all flex items-center gap-1 whitespace-nowrap shrink-0 ${
                          examSubmitted
                            ? 'bg-[#059669] text-white hover:bg-[#047857] ring-2 ring-[#059669]/30 shadow-xs cursor-pointer'
                            : 'bg-slate-100 text-slate-400 border border-slate-200 cursor-not-allowed'
                        }`}
                      >
                        <span>
                          {examIndex < activeExamCards.length - 1
                            ? examSubmitted
                              ? isKo
                                ? `${t.examNextQuestion} (엔터 ↵)`
                                : `${t.examNextQuestion} (Intro ↵)`
                              : t.examNextQuestion
                            : examSubmitted
                              ? isKo
                                ? `${t.examFinishBtn} (엔터 ↵)`
                                : `${t.examFinishBtn} (Intro ↵)`
                              : t.examFinishBtn}
                        </span>
                        <ChevronRight className="w-3.5 h-3.5" />
                      </button>
                    </div>

                    {examSubmitted && (
                      <div
                        className={`rounded-xl border p-5 space-y-2.5 ${
                          examValidation?.isCorrect
                            ? 'bg-[#059669]/[0.05] border-[#059669]/40'
                            : 'bg-slate-50 border-slate-200'
                        }`}
                      >
                        {examValidation && (
                          <div className="flex flex-wrap items-center justify-between gap-2">
                            <div
                              className={`text-xs font-bold ${
                                examValidation.isCorrect
                                  ? 'text-[#059669]'
                                  : 'text-red-600'
                              }`}
                            >
                              {examValidation.isCorrect
                                ? t.typingResultCorrect
                                : t.typingResultWrong}
                            </div>

                            <button
                              type="button"
                              onClick={advanceExamToNextOrFinish}
                              className="px-3.5 py-1.5 text-xs font-bold text-white bg-[#0284C7] hover:bg-[#0369A1] rounded-lg transition-colors flex items-center gap-1 whitespace-nowrap shrink-0"
                            >
                              <span>
                                {examIndex < activeExamCards.length - 1
                                  ? isKo
                                    ? `${t.examNextQuestion} (엔터 ↵)`
                                    : `${t.examNextQuestion} (Intro ↵)`
                                  : isKo
                                    ? `${t.examFinishBtn} (엔터 ↵)`
                                    : `${t.examFinishBtn} (Intro ↵)`}
                              </span>
                              <ChevronRight className="w-3.5 h-3.5" />
                            </button>
                          </div>
                        )}

                        {examValidation && !examValidation.isCorrect && (
                          <div className="text-xs space-y-1 text-slate-700">
                            {examValidation.missingKeywords.length > 0 && (
                              <div>
                                <span className="font-semibold text-red-600 mr-1.5">
                                  {t.typingMissingKeywords}
                                </span>
                                <span className="font-mono-tabular">
                                  {examValidation.missingKeywords.join(', ')}
                                </span>
                              </div>
                            )}
                            {examValidation.missingVerses.length > 0 && (
                              <div>
                                <span className="font-semibold text-red-600 mr-1.5">
                                  {t.typingMissingVerses}
                                </span>
                                <span className="font-mono-tabular">
                                  {examValidation.missingVerses.join(', ')}
                                </span>
                              </div>
                            )}
                          </div>
                        )}

                        <div className="text-xl font-bold text-[#0284C7]">
                          {renderCardAnswerMain(currentExamCard)}
                        </div>
                        <div className="text-xl font-bold text-[#0284C7] font-mono-tabular">
                          {t.labelKeyVerse}: {renderCardVerse(currentExamCard)}
                        </div>

                        {!isKo && (
                          <button
                            type="button"
                            onClick={() =>
                              speakEcuadorSpanish(currentExamCard.question.answerEs)
                            }
                            className="px-2.5 py-1 text-xs font-medium border border-slate-200 bg-white rounded-lg text-slate-700 hover:bg-slate-50 flex items-center gap-1 whitespace-nowrap shrink-0"
                          >
                            <Volume2 className="w-3.5 h-3.5 text-[#059669]" />
                            <span>{t.btnListenAnswerEc}</span>
                          </button>
                        )}

                        <div className="pt-2.5 border-t border-slate-200 flex flex-wrap items-center justify-between gap-2">
                          <div className="flex flex-wrap items-center gap-2">
                            <span className="text-xs text-slate-500 mr-1">
                              {t.examSelfGradeLabel}
                            </span>
                            <button
                              type="button"
                              onClick={() => {
                                setExamResults((prev) => ({
                                  ...prev,
                                  [currentExamCard.uid]: 'correct'
                                }));
                                if (!masteredIds.includes(currentExamCard.question.id)) {
                                  toggleMastered(currentExamCard.question.id);
                                }
                              }}
                              className={`px-3 py-1 text-xs font-semibold rounded-lg border transition-colors whitespace-nowrap shrink-0 ${
                                examResults[currentExamCard.uid] === 'correct'
                                  ? 'bg-[#059669] text-white border-[#059669]'
                                  : 'bg-white text-slate-700 border-slate-300 hover:bg-slate-100'
                              }`}
                            >
                              {t.examGradePass}
                            </button>
                            <button
                              type="button"
                              onClick={() => {
                                setExamResults((prev) => ({
                                  ...prev,
                                  [currentExamCard.uid]: 'review_again'
                                }));
                                if (!bookmarkedIds.includes(currentExamCard.question.id)) {
                                  toggleBookmark(currentExamCard.question.id);
                                }
                              }}
                              className={`px-3 py-1 text-xs font-semibold rounded-lg border transition-colors whitespace-nowrap shrink-0 ${
                                examResults[currentExamCard.uid] === 'review_again'
                                  ? 'bg-[#D97706] text-white border-[#D97706]'
                                  : 'bg-white text-slate-700 border-slate-300 hover:bg-slate-100'
                              }`}
                            >
                              {t.examGradeReview}
                            </button>
                          </div>
                        </div>
                      </div>
                    )}
                  </div>

                  {/* Exam Navigation Footer */}
                  <div className="flex items-center justify-between pt-3 border-t border-slate-200">
                    <button
                      type="button"
                      disabled={examIndex === 0}
                      onClick={() => {
                        setExamIndex((i) => Math.max(0, i - 1));
                        setExamSubmitted(false);
                        setExamValidation(null);
                        setExamInput('');
                        setExamVerseInput('');
                      }}
                      className="px-3.5 py-1.5 text-xs font-semibold border border-slate-200 rounded-lg text-slate-700 hover:bg-slate-50 disabled:opacity-40 whitespace-nowrap shrink-0"
                    >
                      {t.examPrevQuestion}
                    </button>

                    <div className="text-xs font-mono-tabular text-slate-500">
                      {t.examScoreSummary(
                        activeExamCards.filter(
                          (c) => examResults[c.uid] === 'correct'
                        ).length,
                        activeExamCards.filter(
                          (c) => examResults[c.uid] === 'review_again'
                        ).length,
                        activeExamCards.length
                      )}
                    </div>

                    {examIndex < activeExamCards.length - 1 ? (
                      <button
                        type="button"
                        onClick={advanceExamToNextOrFinish}
                        className={`px-3.5 py-1.5 text-xs font-semibold rounded-lg transition-all whitespace-nowrap shrink-0 ${
                          examSubmitted
                            ? 'text-white bg-[#059669] hover:bg-[#047857] ring-2 ring-[#059669]/30'
                            : 'text-white bg-[#0284C7] hover:bg-[#0369A1]'
                        }`}
                      >
                        {examSubmitted
                          ? isKo
                            ? `${t.examNextQuestion} (엔터 ↵)`
                            : `${t.examNextQuestion} (Intro ↵)`
                          : t.examNextQuestion}
                      </button>
                    ) : (
                      <button
                        type="button"
                        onClick={advanceExamToNextOrFinish}
                        className="px-4 py-1.5 text-xs font-bold text-white bg-[#059669] rounded-lg hover:bg-[#047857] whitespace-nowrap shrink-0"
                      >
                        {examSubmitted
                          ? isKo
                            ? `${t.examFinishBtn} (엔터 ↵)`
                            : `${t.examFinishBtn} (Intro ↵)`
                          : t.examFinishBtn}
                      </button>
                    )}
                  </div>
                </div>

                {/* EXAM RESULTS MODAL POPUP */}
                {examShowResults && (() => {
                  const totalCount = activeExamCards.length;
                  const correctCards = activeExamCards.filter(
                    (c) => examResults[c.uid] === 'correct'
                  );
                  const wrongCards = activeExamCards.filter(
                    (c) => examResults[c.uid] !== 'correct'
                  );
                  const correctCount = correctCards.length;
                  const wrongCount = wrongCards.length;
                  const finalPercent =
                    totalCount > 0 ? Math.round((correctCount / totalCount) * 100) : 0;

                  return (
                    <div className="fixed inset-0 z-50 bg-slate-900/50 backdrop-blur-xs flex items-center justify-center p-4">
                      <div className="bg-white border border-slate-200 rounded-2xl w-full max-w-2xl max-h-[90vh] flex flex-col overflow-hidden shadow-xl">
                        {/* Modal Header */}
                        <div className="px-6 py-4 border-b border-slate-200 flex items-center justify-between">
                          <div>
                            <div className="text-xs font-mono-tabular text-slate-400">
                              {examMistakeUids
                                ? t.examReviewMistakesTitle
                                : `${totalCount} Q&A`}
                            </div>
                            <h3 className="font-display text-xl font-bold text-slate-900">
                              {t.examResultsTitle(selectedDay)}
                            </h3>
                          </div>
                          <div className="text-right">
                            <div className="text-xs text-slate-400">
                              {t.examFinalScoreLabel}
                            </div>
                            <div className="font-mono-tabular text-2xl font-bold text-[#0284C7]">
                              {finalPercent}%{' '}
                              <span className="text-sm text-slate-500 font-normal">
                                ({correctCount}/{totalCount})
                              </span>
                            </div>
                          </div>
                        </div>

                        {/* Modal Body */}
                        <div className="p-6 overflow-y-auto space-y-5">
                          {/* Correct / Incorrect Summary Cards */}
                          <div className="grid grid-cols-2 gap-3">
                            <div className="p-4 rounded-xl border border-[#059669]/30 bg-[#059669]/[0.05]">
                              <div className="text-xs font-semibold text-[#059669]">
                                {t.examCorrectCountLabel}
                              </div>
                              <div className="mt-1 font-mono-tabular text-2xl font-bold text-[#059669]">
                                {correctCount}
                              </div>
                            </div>
                            <div className="p-4 rounded-xl border border-[#D97706]/30 bg-[#D97706]/[0.05]">
                              <div className="text-xs font-semibold text-[#D97706]">
                                {t.examWrongCountLabel}
                              </div>
                              <div className="mt-1 font-mono-tabular text-2xl font-bold text-[#D97706]">
                                {wrongCount}
                              </div>
                            </div>
                          </div>

                          {/* Mistakes List or Perfect Score Message */}
                          {wrongCount === 0 ? (
                            <div className="p-6 rounded-xl border border-[#059669]/30 bg-[#059669]/[0.04] text-center space-y-1">
                              <div className="text-lg font-bold text-[#059669]">
                                {t.examPerfectScoreMsg}
                              </div>
                            </div>
                          ) : (
                            <div className="space-y-2.5">
                              <div className="text-xs font-bold text-slate-500">
                                {t.examMistakesListTitle} ({wrongCount})
                              </div>
                              <div className="divide-y divide-slate-100 border border-slate-200 rounded-xl overflow-hidden">
                                {wrongCards.map((card) => (
                                  <div
                                    key={card.uid}
                                    className="p-4 bg-white space-y-1.5"
                                  >
                                    <div className="flex items-center justify-between text-xs text-slate-400 font-mono-tabular">
                                      <span>{card.question.code}</span>
                                      <span className="text-[#D97706] font-semibold">
                                        {t.examWrongBadge}
                                      </span>
                                    </div>
                                    <div className="text-sm font-bold text-slate-900">
                                      {renderCardPrompt(card)}
                                    </div>
                                    <div className="text-xl font-bold text-[#0284C7]">
                                      {renderCardAnswerMain(card)}
                                    </div>
                                    <div className="text-xl font-bold text-[#0284C7] font-mono-tabular">
                                      {t.labelKeyVerse}: {renderCardVerse(card)}
                                    </div>
                                  </div>
                                ))}
                              </div>
                            </div>
                          )}
                        </div>

                        {/* Modal Footer Actions */}
                        <div className="px-6 py-4 border-t border-slate-200 bg-slate-50 flex flex-wrap items-center justify-between gap-2.5">
                          <button
                            type="button"
                            onClick={() => {
                              setExamShowResults(false);
                              setActiveSection('study');
                            }}
                            className="px-3.5 py-2 text-xs font-semibold border border-slate-300 bg-white rounded-lg text-slate-700 hover:bg-slate-100 whitespace-nowrap shrink-0"
                          >
                            {t.examBackToStudyBtn}
                          </button>

                          <div className="flex flex-wrap items-center gap-2">
                            {wrongCount > 0 && (
                              <button
                                type="button"
                                onClick={() => {
                                  const missedUids = wrongCards.map((c) => c.uid);
                                  setExamMistakeUids(missedUids);
                                  setExamResults({});
                                  setExamIndex(0);
                                  setExamSubmitted(false);
                                  setExamValidation(null);
                                  setExamInput('');
                                  setExamVerseInput('');
                                  setExamShowResults(false);
                                }}
                                className="px-4 py-2 text-xs font-bold text-white bg-[#D97706] rounded-lg hover:bg-[#B45309] transition-colors whitespace-nowrap shrink-0"
                              >
                                {t.examReviewMistakesBtn(wrongCount)}
                              </button>
                            )}

                            <button
                              type="button"
                              onClick={() => {
                                setExamMistakeUids(null);
                                setExamResults({});
                                setExamIndex(0);
                                setExamSubmitted(false);
                                setExamValidation(null);
                                setExamInput('');
                                setExamVerseInput('');
                                setExamShowResults(false);
                              }}
                              className="px-4 py-2 text-xs font-bold text-white bg-[#0284C7] rounded-lg hover:bg-[#0369A1] transition-colors whitespace-nowrap shrink-0"
                            >
                              {t.examRetryAllBtn}
                            </button>
                          </div>
                        </div>
                      </div>
                    </div>
                  );
                })()}
              </>
            )}

            {/* ============================================================== */}
            {/* SECTION 3: FULL 99 CATALOG                                     */}
            {/* ============================================================== */}
            {activeSection === 'catalog' && (
              <div className="space-y-3">
                <div className="bg-white border border-slate-200 rounded-xl p-3 flex flex-col sm:flex-row items-stretch sm:items-center justify-between gap-2">
                  <div className="relative flex-1">
                    <Search className="w-4 h-4 text-slate-400 absolute left-3 top-1/2 -translate-y-1/2" />
                    <input
                      type="text"
                      value={searchQuery}
                      onChange={(e) => setSearchQuery(e.target.value)}
                      placeholder={t.catalogSearchPlaceholder}
                      className="w-full pl-9 pr-3 py-1.5 text-xs bg-slate-50 border border-slate-200 rounded-lg focus:outline-none focus:bg-white focus:border-[#0284C7]"
                    />
                  </div>

                  <select
                    value={selectedChapter}
                    onChange={(e) => setSelectedChapter(Number(e.target.value))}
                    className="px-3 py-1.5 text-xs bg-white border border-slate-200 rounded-lg text-slate-700"
                  >
                    <option value={0}>{t.catalogAllChapters}</option>
                    {CHAPTERS.map((ch) => (
                      <option key={ch.id} value={ch.id}>
                        {isKo ? ch.titleKo : ch.titleEs}
                      </option>
                    ))}
                  </select>

                  <select
                    value={catalogStatusFilter}
                    onChange={(e) => setCatalogStatusFilter(e.target.value as any)}
                    className="px-3 py-1.5 text-xs bg-white border border-slate-200 rounded-lg text-slate-700"
                  >
                    <option value="all">
                      {t.catalogFilterAll} ({QUESTIONS.length})
                    </option>
                    <option value="bookmarked">
                      {t.catalogFilterBookmarked} ({bookmarkedIds.length})
                    </option>
                    <option value="mastered">
                      {t.catalogFilterMastered} ({masteredIds.length})
                    </option>
                    <option value="unmastered">
                      {t.catalogFilterUnmastered} ({99 - masteredIds.length})
                    </option>
                  </select>
                </div>

                <div className="bg-white border border-slate-200 rounded-xl divide-y divide-slate-200">
                  {filteredCatalog.length === 0 ? (
                    <div className="p-10 text-center text-sm text-slate-500">
                      {t.catalogEmpty}
                    </div>
                  ) : (
                    filteredCatalog.map((q) => {
                      const isMastered = masteredIds.includes(q.id);
                      const isBookmarked = bookmarkedIds.includes(q.id);
                      const introDay =
                        q.id <= 10 ? 1 : Math.min(19, Math.floor((q.id - 11) / 5) + 2);

                      return (
                        <div
                          key={q.id}
                          className="p-5 hover:bg-slate-50/60 transition-colors"
                        >
                          <div className="flex flex-wrap items-center justify-between gap-2 mb-2">
                            <div className="text-xs text-slate-400 font-mono-tabular">
                              <span className="font-bold text-slate-900">{q.code}</span>
                              <span className="mx-2">·</span>
                              <span>
                                {isKo ? `${introDay}일차` : `Día ${introDay}`}
                              </span>
                              <span className="mx-2">·</span>
                              <span>
                                {isKo ? q.chapterTitleKo : q.chapterTitleEs}
                              </span>
                            </div>

                            <div className="flex items-center gap-1.5">
                              {!isKo && (
                                <button
                                  type="button"
                                  onClick={() =>
                                    speakEcuadorSpanish(
                                      `${q.questionEs} ... ${q.answerEs}`
                                    )
                                  }
                                  className="px-2 py-1 text-xs font-medium border border-slate-200 rounded-md text-slate-600 hover:bg-white flex items-center gap-1 whitespace-nowrap shrink-0"
                                >
                                  <Volume2 className="w-3.5 h-3.5 text-[#0284C7]" />
                                  <span>{t.btnListenPromptEc}</span>
                                </button>
                              )}
                              <button
                                type="button"
                                onClick={() => toggleBookmark(q.id)}
                                className={`px-2 py-1 text-xs font-medium border rounded-md whitespace-nowrap shrink-0 ${
                                  isBookmarked
                                    ? 'border-[#D97706] text-[#D97706] bg-[#D97706]/10'
                                    : 'border-slate-200 text-slate-500'
                                }`}
                              >
                                {t.btnBookmark}
                              </button>
                              <button
                                type="button"
                                onClick={() => toggleMastered(q.id)}
                                className={`px-2 py-1 text-xs font-semibold border rounded-md whitespace-nowrap shrink-0 ${
                                  isMastered
                                    ? 'border-[#059669] bg-[#059669] text-white'
                                    : 'border-slate-200 text-slate-600'
                                }`}
                              >
                                {isMastered ? t.btnMastered : t.btnMarkMastered}
                              </button>
                            </div>
                          </div>

                          <div className="text-base font-bold text-slate-900 mb-1.5">
                            {isKo ? q.questionKo : q.questionEs}
                          </div>
                          <div className="text-xl font-bold text-[#0284C7] mb-1">
                            {isKo ? q.answerKo : q.answerEs}
                          </div>
                          <div className="text-xl font-bold text-[#0284C7] font-mono-tabular">
                            {t.labelKeyVerse}: {isKo ? q.verseKo : q.verseEs}
                          </div>
                        </div>
                      );
                    })
                  )}
                </div>
              </div>
            )}
          </div>

          {/* RIGHT / MINIMAL CONTROL & CURRICULUM DECK (4 cols) */}
          <aside className="lg:col-span-4 space-y-4">
            {/* Day Summary & Reroll Card */}
            <div className="bg-white border border-slate-200 rounded-xl p-4 space-y-3">
              <div className="flex items-center justify-between">
                <h2 className="font-display text-sm font-bold text-slate-900">
                  {t.sidebarDayController(selectedDay)}
                </h2>
                <button
                  type="button"
                  onClick={() => toggleCompleteDay(selectedDay)}
                  className={`px-2.5 py-1 text-xs font-semibold rounded-lg border transition-colors flex items-center gap-1 whitespace-nowrap shrink-0 ${
                    completedDays.includes(selectedDay)
                      ? 'border-[#059669] bg-[#059669] text-white'
                      : 'border-slate-200 text-slate-700 hover:bg-slate-50'
                  }`}
                >
                  <CheckCircle2 className="w-3.5 h-3.5" />
                  <span>
                    {completedDays.includes(selectedDay)
                      ? t.btnCompletedDay(selectedDay)
                      : t.btnCompleteDay(selectedDay)}
                  </span>
                </button>
              </div>

              <div className="text-xs text-slate-600 space-y-1.5 pt-2 border-t border-slate-100">
                <div className="flex justify-between">
                  <span className="text-slate-400">{t.labelNewRange}</span>
                  <span className="font-mono-tabular font-semibold text-slate-900">
                    {currentSchedule.newConceptIds.length > 0
                      ? `Q${currentSchedule.newConceptIds[0]}~Q${
                          currentSchedule.newConceptIds[
                            currentSchedule.newConceptIds.length - 1
                          ]
                        }`
                      : t.labelAllRangeReview}
                  </span>
                </div>
                <div className="flex justify-between">
                  <span className="text-slate-400">{t.labelReviewPoolRange}</span>
                  <span className="font-mono-tabular font-semibold text-slate-900">
                    {currentSchedule.learnedPoolIds.length > 0
                      ? `Q1~Q${
                          currentSchedule.learnedPoolIds[
                            currentSchedule.learnedPoolIds.length - 1
                          ]
                        }`
                      : t.labelFirstDayNoReview}
                  </span>
                </div>
                <div className="flex justify-between">
                  <span className="text-slate-400">
                    {t.labelSelectedReviewConcepts}
                  </span>
                  <span className="font-mono-tabular font-semibold text-[#0284C7]">
                    {(dailyReviews[selectedDay] || []).length > 0
                      ? (dailyReviews[selectedDay] || [])
                          .map((id) => `Q${id}`)
                          .join(', ')
                      : '-'}
                  </span>
                </div>
              </div>

              {selectedDay >= 2 && (
                <div className="pt-2.5 border-t border-slate-100 space-y-2">
                  <label className="flex items-center gap-2 text-xs text-slate-600 cursor-pointer">
                    <input
                      type="checkbox"
                      checked={prioritizeWeakInRandom}
                      onChange={(e) => setPrioritizeWeakInRandom(e.target.checked)}
                      className="rounded border-slate-300 text-[#0284C7] focus:ring-[#0284C7]"
                    />
                    <span>{t.checkboxPrioritizeWeak}</span>
                  </label>

                  <button
                    type="button"
                    onClick={handleRerollReviews}
                    className="w-full py-1.5 px-3 text-xs font-semibold text-slate-800 bg-slate-100 hover:bg-slate-200/80 rounded-lg transition-colors flex items-center justify-center gap-1.5"
                  >
                    <Shuffle className="w-3.5 h-3.5 text-[#0284C7]" />
                    <span>{t.btnRerollReview}</span>
                  </button>
                </div>
              )}
            </div>

            {/* 20-Day Schedule Selector */}
            <div className="bg-white border border-slate-200 rounded-xl p-4 space-y-3">
              <div className="flex items-center justify-between">
                <div>
                  <h3 className="font-display text-sm font-bold text-slate-900">
                    {t.curriculumTitle}
                  </h3>
                  <p className="text-[11px] text-slate-400">
                    {t.curriculumSubtitle}
                  </p>
                </div>
                <span className="text-xs font-mono-tabular font-semibold text-[#059669]">
                  {completedDays.length}/20
                </span>
              </div>

              <div className="max-h-[380px] overflow-y-auto divide-y divide-slate-100 border border-slate-200 rounded-lg">
                {DAY_SCHEDULES.map((sched) => {
                  const isSelected = sched.day === selectedDay;
                  const isDone = completedDays.includes(sched.day);
                  const newRangeText =
                    sched.newConceptIds.length > 0
                      ? `Q${sched.newConceptIds[0]}~Q${
                          sched.newConceptIds[sched.newConceptIds.length - 1]
                        }`
                      : t.labelAllRangeReview;
                  const revRangeText =
                    sched.reviewConceptCount > 0
                      ? isKo
                        ? `+ 복습 ${sched.reviewConceptCount}`
                        : `+ Repaso ${sched.reviewConceptCount}`
                      : '';

                  return (
                    <button
                      key={sched.day}
                      type="button"
                      onClick={() => {
                        setSelectedDay(sched.day);
                        setFocusCardIndex(0);
                        setExamIndex(0);
                        setExamSubmitted(false);
                        setExamValidation(null);
                        setExamInput('');
                        setExamVerseInput('');
                        setExamShowResults(false);
                        setExamMistakeUids(null);
                        setExamResults({});
                      }}
                      className={`w-full text-left px-3 py-2 transition-colors flex items-center justify-between gap-2 ${
                        isSelected ? 'bg-[#0284C7]/10' : 'hover:bg-slate-50'
                      }`}
                    >
                      <div className="flex items-center gap-2 min-w-0">
                        <span
                          className={`text-xs font-mono-tabular font-bold ${
                            isSelected ? 'text-[#0284C7]' : 'text-slate-900'
                          }`}
                        >
                          {isKo
                            ? `${String(sched.day).padStart(2, '0')}일차`
                            : `Día ${String(sched.day).padStart(2, '0')}`}
                        </span>
                        <span className="text-xs text-slate-600 font-mono-tabular truncate">
                          {newRangeText} {revRangeText}
                        </span>
                      </div>

                      {isDone && (
                        <span className="text-xs font-mono-tabular font-semibold text-[#059669] shrink-0">
                          ✓
                        </span>
                      )}
                    </button>
                  );
                })}
              </div>

              <div className="pt-1 flex items-center justify-between text-[11px] text-slate-400">
                <span>{t.autoSaveNote}</span>
                <button
                  type="button"
                  onClick={() => {
                    setCompletedDays([]);
                    setMasteredIds([]);
                    setBookmarkedIds([]);
                    setDailyReviews({});
                    setTypingEntries({});
                    setSelectedDay(1);
                  }}
                  className="text-slate-400 hover:text-red-600 flex items-center gap-1 whitespace-nowrap shrink-0"
                >
                  <RotateCcw className="w-3 h-3" />
                  <span>{t.btnResetProgress}</span>
                </button>
              </div>
            </div>
          </aside>
        </div>
      </main>

      {/* Minimal Footer */}
      <footer className="mt-8 border-t border-slate-200 bg-white py-4 px-6">
        <div className="max-w-[1280px] mx-auto flex items-center justify-between gap-4 text-xs text-slate-400">
          <div>{t.footerText}</div>
          <button
            type="button"
            onClick={downloadStandaloneChromeHtml}
            className="text-[#0284C7] hover:underline font-medium"
          >
            {t.btnDownloadHtml}
          </button>
        </div>
      </footer>
    </div>
  );
}
