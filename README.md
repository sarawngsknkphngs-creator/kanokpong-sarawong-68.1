<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ระบบจองห้องประชุม Smart Meeting Room</title>
    
    <!-- Tailwind CSS for UI -->
    <script src="https://tailwindcss.com"></script>
    <!-- Alpine.js for App Logic -->
    <script defer src="https://jsdelivr.net"></script>
    <!-- Chart.js for Dashboard Charts -->
    <script src="https://jsdelivr.net"></script>
    <!-- FullCalendar CSS & JS -->
    <link href="https://jsdelivr.net" rel="stylesheet">
    <script src="https://jsdelivr.net"></script>

    <style>
        .fc-theme-standard td, .fc-theme-standard th { border-color: #e2e8f0; }
        .fc .fc-button-primary { background-color: #1e3a8a; border-color: #1e3a8a; }
        .fc .fc-button-primary:hover { background-color: #1d4ed8; border-color: #1d4ed8; }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 font-sans" x-data="bookingApp()">

    <!-- Top Navigation Bar -->
    <header class="bg-white border-b border-slate-200 sticky top-0 z-40 shadow-sm">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between">
            <div class="flex items-center space-x-3">
                <div class="bg-blue-900 text-white p-2 rounded-lg shadow-md">
                    <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 002-2H5a2 2 0 00-2 2v12a2 2 0 002 2z"></path></svg>
                </div>
                <span class="text-xl font-bold text-blue-950 tracking-tight">Smart Meeting Room</span>
            </div>
            
            <!-- ส่วนแสดงรูปภาพโปรไฟล์ 2 รูปคู่กันตามที่ระบุในเงื่อนไข -->
            <div class="flex items-center space-x-3 bg-slate-50 p-1.5 pr-4 rounded-full border border-slate-200 shadow-inner">
                <div class="flex -space-x-2">
                    <!-- รูปที่ 1: เปลี่ยน URL รูปภาพตัวเองตรงนี้ -->
                    <img class="inline-block h-9 w-9 rounded-full ring-2 ring-white object-cover shadow" 
                         src="https://unsplash.com" alt="รูปโปรไฟล์ 1">
                    <!-- รูปที่ 2: เปลี่ยน URL รูปภาพตัวเองตรงนี้ -->
                    <img class="inline-block h-9 w-9 rounded-full ring-2 ring-white object-cover shadow" 
                         src="https://unsplash.com" alt="รูปโปรไฟล์ 2">
                </div>
                <div class="text-xs">
                    <p class="font-semibold text-slate-700 leading-none">ผู้ดูแลระบบ</p>
                    <p class="text-emerald-600 font-medium mt-0.5 flex items-center"><span class="w-1.5 h-1.5 bg-emerald-500 rounded-full inline-block mr-1 animate-pulse"></span> ออนไลน์</p>
                </div>
            </div>
        </div>
    </header>

    <!-- Real-time Notification Pop-up -->
    <div class="fixed bottom-5 right-5 z-50 space-y-2 max-w-sm w-full">
        <template x-for="note in notifications" :key="note.id">
            <div x-transition class="bg-slate-900 text-white p-4 rounded-xl shadow-2xl flex items-start space-x-3 border border-slate-800 animate-bounce-short">
                <div class="bg-blue-500 p-1.5 rounded-lg mt-0.5">
                    <svg class="w-4 h-4 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 17h5l-1.405-1.405A2.032 2.032 0 0118 14.158V11a6.002 6.002 0 00-4-5.659V5a2 2 0 10-4 0v.341C7.67 6.165 6 8.388 6 11v3.159c0 .538-.214 1.055-.595 1.436L4 17h5m6 0v1a3 3 0 11-6 0v-1m6 0H9"></path></svg>
                </div>
                <div class="flex-1">
                    <h4 class="font-bold text-sm text-blue-400" x-text="note.title"></h4>
                    <p class="text-xs text-slate-300 mt-0.5" x-text="note.message"></p>
                </div>
                <button @click="removeNotification(note.id)" class="text-slate-400 hover:text-white transition">&times;</button>
            </div>
        </template>
    </div>

    <!-- Main Content Layout -->
    <main class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
        
        <!-- 1. DASHBOARD SECTION -->
        <section class="mb-8">
            <h2 class="text-lg font-bold text-slate-900 mb-4 flex items-center">
                <span class="w-2.5 h-5 bg-blue-900 rounded mr-2 inline-block"></span>แดชบอร์ดสรุปสถิติการใช้งานเรียลไทม์
            </h2>
            
            <!-- Stats Grid -->
            <div class="grid grid-cols-1 md:grid-cols-4 gap-5 mb-6">
                <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">จองทั้งหมดวันนี้</p>
                        <h3 class="text-3xl font-black text-blue-950 mt-1" x-text="stats.todayBookings">0</h3>
                    </div>
                    <div class="bg-blue-50 p-3 rounded-xl text-blue-900"><svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2m-3 7h3m-3 4h3m-6-4h.01M9 16h.01"></path></svg></div>
                </div>
                <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">ห้องว่างตอนนี้</p>
                        <h3 class="text-3xl font-black text-emerald-600 mt-1" x-text="stats.availableRooms">0</h3>
                    </div>
                    <div class="bg-emerald-50 p-3 rounded-xl text-emerald-600"><svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0x"></path></svg></div>
                </div>
                <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">กำลังใช้งานอยู่</p>
                        <h3 class="text-3xl font-black text-amber-500 mt-1" x-text="stats.occupiedRooms">0</h3>
                    </div>
                    <div class="bg-amber-50 p-3 rounded-xl text-amber-500"><svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg></div>
                </div>
                <div class="bg-white p-5 rounded-2xl border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <p class="text-xs font-semibold text-slate-500 uppercase tracking-wider">อัตราการใช้งานห้อง</p>
                        <h3 class="text-3xl font-black text-slate-800 mt-1" x-text="stats.utilizationRate + '%'">0%</h3>
                    </div>
                    <div class="bg-slate-100 p-3 rounded-xl text-slate-600"><svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 3.055A9.001 9.001 0 1020.945 13H11V3.055z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20.488 9H15V3.512A9.025 9.025 0 0120.488 9z"></path></svg></div>
                </div>
            </div>

            <!-- Charts Container -->
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm grid grid-cols-1 md:grid-cols-2 gap-8">
                <div class="h-64 flex flex-col justify-between">
                    <h3 class="text-sm font-bold text-slate-700 mb-2">สถิติความถี่การจองแยกตามห้องประชุม</h3>
                    <div class="relative flex-1"><canvas id="roomChart"></canvas></div>
                </div>
                <div class="h-64 flex flex-col justify-between">
                    <h3 class="text-sm font-bold text-slate-700 mb-2">ช่วงเวลาที่มีการจองหนาแน่นที่สุด (ชั่วโมง)</h3>
                    <div class="relative flex-1"><canvas id="timeChart"></canvas></div>
                </div>
            </div>
        </section>

        <!-- 2. CALENDAR & BOOKING INTERACTION -->
        <div class="grid grid-cols-1 lg:grid-cols-3 gap-8 mb-8">
            <!-- Left & Center Side: Calendar -->
            <div class="lg:col-span-2 bg-white p-6 rounded-2xl border border-slate-200 shadow-sm">
                <h2 class="text-lg font-bold text-slate-950 mb-4 flex items-center">
                    <span class="w-2.5 h-5 bg-blue-900 rounded mr-2 inline-block"></span>ปฏิทินตรวจสอบสถานะห้องประชุม
                </h2>
                <div id="calendar"></div>
            </div>

            <!-- Right Side: Booking Form -->
            <div class="bg-white p-6 rounded-2xl border border-slate-200 shadow-sm h-fit sticky top-24">
                <h2 class="text-lg font-bold text-slate-950 mb-4 flex items-center">
                    <span class="w-2.5 h-5 bg-blue-900 rounded mr-2 inline-block"></span>แบบฟอร์มจองห้องประชุม
                </h2>
                <form @submit.prevent="submitBooking" class="space-y-4">
                    <div>
