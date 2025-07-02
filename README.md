# private_reservation
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>服务预约系统</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdn.jsdelivr.net/npm/font-awesome@4.7.0/css/font-awesome.min.css" rel="stylesheet">
    
    <!-- Tailwind配置 -->
    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: '#3B82F6',
                        secondary: '#10B981',
                        accent: '#EF4444',
                        dark: '#1F2937',
                        light: '#F3F4F6'
                    },
                    fontFamily: {
                        sans: ['Inter', 'system-ui', 'sans-serif'],
                    },
                }
            }
        }
    </script>
    
    <style type="text/tailwindcss">
        @layer utilities {
            .content-auto {
                content-visibility: auto;
            }
            .card-shadow {
                box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1), 0 4px 6px -2px rgba(0, 0, 0, 0.05);
            }
            .hover-scale {
                transition: transform 0.3s ease;
            }
            .hover-scale:hover {
                transform: scale(1.03);
            }
            .package-card {
                @apply border-2 rounded-xl p-5 text-center cursor-pointer transition-all hover-scale;
            }
            .time-slot-item {
                @apply border rounded-lg p-3 text-center cursor-pointer transition-all hover:bg-gray-100;
            }
            .day-item {
                @apply border rounded-lg p-4 text-center cursor-pointer transition-all hover:bg-gray-100;
            }
            .selected {
                @apply border-accent bg-accent/10;
            }
            .time-selected {
                @apply bg-secondary text-white border-secondary;
            }
            .date-selected {
                @apply bg-primary text-white border-primary;
            }
        }
    </style>
</head>
<body class="bg-gray-50 min-h-screen">
    <!-- 导航栏 -->
    <nav class="bg-white shadow-md sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex justify-between h-16">
                <div class="flex items-center">
                    <span class="text-xl font-bold text-primary">
                        <i class="fa fa-calendar-check-o mr-2"></i>服务预约
                    </span>
                </div>
                <div class="flex items-center">
                    <button class="text-gray-600 hover:text-primary transition-colors mr-4">
                        <i class="fa fa-question-circle"></i> 帮助
                    </button>
                    <button class="bg-primary hover:bg-primary/90 text-white px-4 py-2 rounded-lg transition-colors">
                        <i class="fa fa-user-circle mr-1"></i> 登录
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- 主内容区 -->
    <main class="max-w-7xl mx-auto py-8 px-4 sm:px-6 lg:px-8">
        <div class="text-center mb-8">
            <h1 class="text-[clamp(1.8rem,4vw,2.5rem)] font-bold text-dark mb-3">专业服务预约</h1>
            <p class="text-gray-600 max-w-2xl mx-auto">选择适合您的套餐和时间，我们将为您提供最优质的服务体验</p>
        </div>

        <div class="bg-white rounded-2xl shadow-xl p-6 md:p-8 mb-8">
            <!-- 步骤指示器 -->
            <div class="flex justify-between mb-10 relative">
                <div class="absolute top-1/2 left-0 right-0 h-1 bg-gray-200 -translate-y-1/2 z-0"></div>
                <div class="absolute top-1/2 left-0 right-1/2 h-1 bg-primary -translate-y-1/2 z-10 transition-all duration-500" id="progress-bar"></div>
                
                <div class="relative z-20 flex flex-col items-center">
                    <div class="w-10 h-10 rounded-full bg-primary text-white flex items-center justify-center">
                        <i class="fa fa-list-ul"></i>
                    </div>
                    <span class="mt-2 text-sm font-medium text-primary">选择套餐</span>
                </div>
                
                <div class="relative z-20 flex flex-col items-center">
                    <div class="w-10 h-10 rounded-full bg-gray-200 text-gray-500 flex items-center justify-center transition-all duration-300" id="step-2-icon">
                        <i class="fa fa-calendar"></i>
                    </div>
                    <span class="mt-2 text-sm font-medium text-gray-500 transition-all duration-300" id="step-2-text">预约时间</span>
                </div>
                
                <div class="relative z-20 flex flex-col items-center">
                    <div class="w-10 h-10 rounded-full bg-gray-200 text-gray-500 flex items-center justify-center transition-all duration-300" id="step-3-icon">
                        <i class="fa fa-check"></i>
                    </div>
                    <span class="mt-2 text-sm font-medium text-gray-500 transition-all duration-300" id="step-3-text">确认提交</span>
                </div>
            </div>

            <!-- 表单内容 -->
            <form id="booking-form">
                <!-- 步骤1：选择套餐 -->
                <div id="step-1-content">
                    <h2 class="text-2xl font-bold text-dark mb-6">选择服务套餐</h2>
                    <p class="text-gray-600 mb-8">我们提供多种套餐选择，满足不同需求，请选择最适合您的套餐</p>
                    
                    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 mb-8" id="packages-container">
                        <!-- 套餐卡片 -->
                        <div class="package-card border-gray-200 hover:border-primary" data-price="488">
                            <div class="mb-4">
                                <span class="inline-block px-3 py-1 bg-primary/10 text-primary rounded-full text-sm font-medium">
                                    基础款
                                </span>
                            </div>
                            <h3 class="text-xl font-bold mb-2">488套餐</h3>
                            <div class="text-3xl font-bold text-accent mb-4">¥488</div>
                            <p class="text-gray-600 mb-4">适合初次体验的基础服务套餐</p>
                            <ul class="text-left mb-6 space-y-2">
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>3次基础服务</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>基础项目支持</span>
                                </li>
                                <li class="flex items-start text-gray-400">
                                    <i class="fa fa-times mt-1 mr-2"></i>
                                    <span>专属顾问服务</span>
                                </li>
                                <li class="flex items-start text-gray-400">
                                    <i class="fa fa-times mt-1 mr-2"></i>
                                    <span>优先处理权</span>
                                </li>
                            </ul>
                        </div>
                        
                        <div class="package-card border-gray-200 hover:border-primary" data-price="666">
                            <div class="mb-4">
                                <span class="inline-block px-3 py-1 bg-primary/10 text-primary rounded-full text-sm font-medium">
                                    标准款
                                </span>
                            </div>
                            <h3 class="text-xl font-bold mb-2">666套餐</h3>
                            <div class="text-3xl font-bold text-accent mb-4">¥666</div>
                            <p class="text-gray-600 mb-4">适合常规需求的标准服务套餐</p>
                            <ul class="text-left mb-6 space-y-2">
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>5次标准服务</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>标准项目支持</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>基础顾问服务</span>
                                </li>
                                <li class="flex items-start text-gray-400">
                                    <i class="fa fa-times mt-1 mr-2"></i>
                                    <span>优先处理权</span>
                                </li>
                            </ul>
                        </div>
                        
                        <div class="package-card border-gray-200 hover:border-primary" data-price="888">
                            <div class="mb-4">
                                <span class="inline-block px-3 py-1 bg-primary/10 text-primary rounded-full text-sm font-medium">
                                    高级款
                                </span>
                            </div>
                            <h3 class="text-xl font-bold mb-2">888套餐</h3>
                            <div class="text-3xl font-bold text-accent mb-4">¥888</div>
                            <p class="text-gray-600 mb-4">适合有一定需求的高级服务套餐</p>
                            <ul class="text-left mb-6 space-y-2">
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>8次高级服务</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>高级项目支持</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>专属顾问服务</span>
                                </li>
                                <li class="flex items-start text-gray-400">
                                    <i class="fa fa-times mt-1 mr-2"></i>
                                    <span>优先处理权</span>
                                </li>
                            </ul>
                        </div>
                        
                        <div class="package-card border-gray-200 hover:border-primary" data-price="1222">
                            <div class="mb-4">
                                <span class="inline-block px-3 py-1 bg-primary/10 text-primary rounded-full text-sm font-medium">
                                    豪华款
                                </span>
                            </div>
                            <h3 class="text-xl font-bold mb-2">1222套餐</h3>
                            <div class="text-3xl font-bold text-accent mb-4">¥1222</div>
                            <p class="text-gray-600 mb-4">适合高要求的豪华服务套餐</p>
                            <ul class="text-left mb-6 space-y-2">
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>12次豪华服务</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>豪华项目支持</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>专属顾问服务</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>优先处理权</span>
                                </li>
                            </ul>
                        </div>
                        
                        <div class="package-card border-gray-200 hover:border-primary" data-price="1666">
                            <div class="mb-4">
                                <span class="inline-block px-3 py-1 bg-primary/10 text-primary rounded-full text-sm font-medium">
                                    尊享款
                                </span>
                            </div>
                            <h3 class="text-xl font-bold mb-2">1666套餐</h3>
                            <div class="text-3xl font-bold text-accent mb-4">¥1666</div>
                            <p class="text-gray-600 mb-4">尊享全方位服务体验</p>
                            <ul class="text-left mb-6 space-y-2">
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>18次尊享服务</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>尊享项目支持</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>专属高级顾问</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>VIP优先处理</span>
                                </li>
                            </ul>
                        </div>
                        
                        <div class="package-card border-gray-200 hover:border-primary" data-price="1888">
                            <div class="mb-4">
                                <span class="inline-block px-3 py-1 bg-primary/10 text-primary rounded-full text-sm font-medium">
                                    至尊款
                                </span>
                            </div>
                            <h3 class="text-xl font-bold mb-2">1888套餐</h3>
                            <div class="text-3xl font-bold text-accent mb-4">¥1888</div>
                            <p class="text-gray-600 mb-4">至尊级全方位服务体验</p>
                            <ul class="text-left mb-6 space-y-2">
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>无限次服务</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>顶级项目支持</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>专属顶级顾问</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>24/7专属服务</span>
                                </li>
                            </ul>
                        </div>
                        
                        <div class="package-card border-gray-200 hover:border-primary" data-price="9999">
                            <div class="mb-4">
                                <span class="inline-block px-3 py-1 bg-accent/10 text-accent rounded-full text-sm font-medium">
                                    终身服务
                                </span>
                            </div>
                            <h3 class="text-xl font-bold mb-2">终身套餐</h3>
                            <div class="text-3xl font-bold text-accent mb-4">¥9999</div>
                            <p class="text-gray-600 mb-4">一次性付费，终身享受服务</p>
                            <ul class="text-left mb-6 space-y-2">
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>终身无限次服务</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>全部项目支持</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>专属首席顾问</span>
                                </li>
                                <li class="flex items-start">
                                    <i class="fa fa-check text-secondary mt-1 mr-2"></i>
                                    <span>终身VIP专属服务</span>
                                </li>
                            </ul>
                        </div>
                    </div>
                    
                    <div class="flex justify-end">
                        <button type="button" id="next-to-step-2" class="bg-primary hover:bg-primary/90 text-white px-8 py-3 rounded-lg transition-all flex items-center shadow-lg hover:shadow-primary/30">
                            <span>下一步</span>
                            <i class="fa fa-arrow-right ml-2"></i>
                        </button>
                    </div>
                </div>
                
                <!-- 步骤2：预约时间 -->
                <div id="step-2-content" class="hidden">
                    <h2 class="text-2xl font-bold text-dark mb-6">预约服务时间</h2>
                    <p class="text-gray-600 mb-8">请选择您方便的日期和时间，我们将为您安排专业的服务人员</p>
                    
                    <div class="mb-8">
                        <h3 class="text-lg font-semibold mb-4">选择日期</h3>
                        <div class="grid grid-cols-2 sm:grid-cols-4 md:grid-cols-7 gap-3" id="date-selection">
                            <!-- 日期选择 -->
                        </div>
                    </div>
                    
                    <div class="mb-8">
                        <h3 class="text-lg font-semibold mb-4">选择时间</h3>
                        <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-3" id="time-selection">
                            <!-- 时间选择 -->
                        </div>
                    </div>
                    
                    <div class="mb-8">
                        <h3 class="text-lg font-semibold mb-4">个人信息</h3>
                        
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-4 mb-4">
                            <div>
                                <label for="fullname" class="block text-sm font-medium text-gray-700 mb-1">姓名</label>
                                <input type="text" id="fullname" name="fullname" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all" placeholder="请输入您的姓名">
                                <p class="error-message text-accent text-sm mt-1 hidden">请输入您的姓名</p>
                            </div>
                            
                            <div>
                                <label for="phone" class="block text-sm font-medium text-gray-700 mb-1">手机号码</label>
                                <input type="tel" id="phone" name="phone" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all" placeholder="请输入您的手机号码">
                                <p class="error-message text-accent text-sm mt-1 hidden">请输入有效的手机号码</p>
                            </div>
                        </div>
                        
                        <div class="mb-4">
                            <label for="email" class="block text-sm font-medium text-gray-700 mb-1">电子邮箱 (选填)</label>
                            <input type="email" id="email" name="email" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all" placeholder="请输入您的电子邮箱">
                        </div>
                        
                        <div class="mb-4">
                            <label for="notes" class="block text-sm font-medium text-gray-700 mb-1">备注信息 (选填)</label>
                            <textarea id="notes" name="notes" rows="3" class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-primary focus:border-primary transition-all" placeholder="如有特殊需求，请在此说明"></textarea>
                        </div>
                    </div>
                    
                    <div class="flex justify-between">
                        <button type="button" id="back-to-step-1" class="bg-gray-200 hover:bg-gray-300 text-gray-700 px-6 py-3 rounded-lg transition-all flex items-center">
                            <i class="fa fa-arrow-left mr-2"></i>
                            <span>上一步</span>
                        </button>
                        
                        <button type="button" id="next-to-step-3" class="bg-primary hover:bg-primary/90 text-white px-8 py-3 rounded-lg transition-all flex items-center shadow-lg hover:shadow-primary/30">
                            <span>确认提交</span>
                            <i class="fa fa-check ml-2"></i>
                        </button>
                    </div>
                </div>
                
                <!-- 步骤3：确认信息 -->
                <div id="step-3-content" class="hidden">
                    <div class="text-center mb-8">
                        <div class="inline-flex items-center justify-center w-16 h-16 rounded-full bg-primary/10 text-primary mb-4">
                            <i class="fa fa-check text-2xl"></i>
                        </div>
                        <h2 class="text-2xl font-bold text-dark mb-2">请确认您的预约信息</h2>
                        <p class="text-gray-600">请仔细核对以下信息，确认无误后提交预约</p>
                    </div>
                    
                    <div class="bg-gray-50 rounded-xl p-6 mb-8">
                        <h3 class="text-lg font-semibold mb-4 pb-2 border-b border-gray-200">预约详情</h3>
                        
                        <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
                            <div>
                                <div class="mb-4">
                                    <p class="text-sm text-gray-500 mb-1">选择的套餐</p>
                                    <p id="summary-package" class="font-medium">488套餐</p>
                                </div>
                                
                                <div class="mb-4">
                                    <p class="text-sm text-gray-500 mb-1">预约日期</p>
                                    <p id="summary-date" class="font-medium">2025年7月3日</p>
                                </div>
                                
                                <div class="mb-4">
                                    <p class="text-sm text-gray-500 mb-1">预约时间</p>
                                    <p id="summary-time" class="font-medium">09:00 - 09:30</p>
                                </div>
                            </div>
                            
                            <div>
                                <div class="mb-4">
                                    <p class="text-sm text-gray-500 mb-1">姓名</p>
                                    <p id="summary-name" class="font-medium">张先生</p>
                                </div>
                                
                                <div class="mb-4">
                                    <p class="text-sm text-gray-500 mb-1">手机号码</p>
                                    <p id="summary-phone" class="font-medium">13800138000</p>
                                </div>
                                
                                <div class="mb-4">
                                    <p class="text-sm text-gray-500 mb-1">服务价格</p>
                                    <p id="summary-price" class="font-medium text-accent">¥488</p>
                                </div>
                            </div>
                        </div>
                    </div>
                    
                    <div class="mb-8">
                        <label class="flex items-start">
                            <input type="checkbox" id="agreement" class="mt-1 h-4 w-4 text-primary focus:ring-primary border-gray-300 rounded">
                            <span class="ml-2 text-sm text-gray-600">
                                我已阅读并同意<a href="#" class="text-primary hover:underline">服务条款</a>和<a href="#" class="text-primary hover:underline">隐私政策</a>
                            </span>
                        </label>
                        <p id="agreement-error" class="text-accent text-sm mt-1 hidden">请同意服务条款和隐私政策</p>
                    </div>
                    
                    <div class="flex justify-between">
                        <button type="button" id="back-to-step-2" class="bg-gray-200 hover:bg-gray-300 text-gray-700 px-6 py-3 rounded-lg transition-all flex items-center">
                            <i class="fa fa-arrow-left mr-2"></i>
                            <span>上一步</span>
                        </button>
                        
                        <button type="button" id="submit-booking" class="bg-accent hover:bg-accent/90 text-white px-8 py-3 rounded-lg transition-all flex items-center shadow-lg hover:shadow-accent/30">
                            <span>提交预约</span>
                            <i class="fa fa-paper-plane ml-2"></i>
                        </button>
                    </div>
                </div>
                
                <!-- 预约成功 -->
                <div id="booking-success" class="hidden text-center py-8">
                    <div class="inline-flex items-center justify-center w-20 h-20 rounded-full bg-secondary/10 text-secondary mb-6">
                        <i class="fa fa-check-circle text-3xl"></i>
                    </div>
                    <h2 class="text-2xl font-bold text-dark mb-3">预约提交成功！</h2>
                    <p class="text-gray-600 max-w-md mx-auto mb-8">
                        您的预约请求已成功提交，我们的客服人员将在24小时内与您联系确认详情
                    </p>
                    
                    <div class="bg-gray-50 rounded-xl p-6 max-w-md mx-auto mb-8 text-left">
                        <h3 class="text-lg font-semibold mb-4 pb-2 border-b border-gray-200">预约信息</h3>
                        
                        <div class="space-y-3">
                            <div class="flex justify-between">
                                <span class="text-gray-600">预约编号</span>
                                <span id="booking-id" class="font-medium">SB20250703001</span>
                            </div>
                            <div class="flex justify-between">
                                <span class="text-gray-600">套餐类型</span>
                                <span id="success-package" class="font-medium">488套餐</span>
                            </div>
                            <div class="flex justify-between">
                                <span class="text-gray-600">预约时间</span>
                                <span id="success-time" class="font-medium">2025年7月3日 09:00</span>
                            </div>
                            <div class="flex justify-between">
                                <span class="text-gray-600">服务价格</span>
                                <span id="success-price" class="font-medium text-accent">¥488</span>
                            </div>
                        </div>
                    </div>
                    
                    <button type="button" id="new-booking" class="bg-primary hover:bg-primary/90 text-white px-8 py-3 rounded-lg transition-all flex items-center mx-auto shadow-lg hover:shadow-primary/30">
                        <i class="fa fa-plus mr-2"></i>
                        <span>创建新预约</span>
                    </button>
                </div>
            </form>
        </div>
        
        <!-- 服务介绍 -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-8">
            <div class="bg-white rounded-xl p-6 shadow-md hover:shadow-lg transition-all">
                <div class="w-12 h-12 rounded-full bg-primary/10 text-primary flex items-center justify-center mb-4">
                    <i class="fa fa-clock-o text-xl"></i>
                </div>
                <h3 class="text-xl font-bold mb-2">准时服务</h3>
                <p class="text-gray-600">我们承诺准时为您提供服务，如有特殊情况会提前与您联系沟通。</p>
            </div>
            
            <div class="bg-white rounded-xl p-6 shadow-md hover:shadow-lg transition-all">
                <div class="w-12 h-12 rounded-full bg-primary/10 text-primary flex items-center justify-center mb-4">
                    <i class="fa fa-certificate text-xl"></i>
                </div>
                <h3 class="text-xl font-bold mb-2">专业团队</h3>
                <p class="text-gray-600">我们的服务人员均经过专业培训，拥有丰富的经验和专业知识。</p>
            </div>
            
            <div class="bg-white rounded-xl p-6 shadow-md hover:shadow-lg transition-all">
                <div class="w-12 h-12 rounded-full bg-primary/10 text-primary flex items-center justify-center mb-4">
                    <i class="fa fa-shield text-xl"></i>
                </div>
                <h3 class="text-xl font-bold mb-2">品质保障</h3>
                <p class="text-gray-600">我们提供优质的服务和产品，确保您获得满意的体验和效果。</p>
            </div>
        </div>
        
        <!-- 常见问题 -->
        <div class="bg-white rounded-xl p-6 shadow-md mb-8">
            <h2 class="text-2xl font-bold text-dark mb-6">常见问题</h2>
            
            <div class="space-y-4">
                <div class="border-b border-gray-100 pb-4">
                    <button class="flex justify-between items-center w-full text-left" onclick="toggleFAQ(this)">
                        <h3 class="text-lg font-medium">如何修改已提交的预约？</h3>
                        <i class="fa fa-chevron-down text-gray-400 transition-transform"></i>
                    </button>
                    <div class="faq-answer hidden mt-2 text-gray-600">
                        您可以在预约成功后，通过"我的预约"页面找到对应的预约记录，点击"修改"按钮进行时间或服务内容的调整。如需修改套餐类型，请联系客服人员协助处理。
                    </div>
                </div>
                
                <div class="border-b border-gray-100 pb-4">
                    <button class="flex justify-between items-center w-full text-left" onclick="toggleFAQ(this)">
                        <h3 class="text-lg font-medium">预约后可以取消吗？有什么限制？</h3>
                        <i class="fa fa-chevron-down text-gray-400 transition-transform"></i>
                    </button>
                    <div class="faq-answer hidden mt-2 text-gray-600">
                        您可以在预约服务开始前24小时取消预约，不会产生任何费用。如在服务开始前24小时内取消，将收取预约金额的30%作为手续费。如未按时参加服务且未提前取消，将收取全额费用。
                    </div>
                </div>
                
                <div class="border-b border-gray-100 pb-4">
                    <button class="flex justify-between items-center w-full text-left" onclick="toggleFAQ(this)">
                        <h3 class="text-lg font-medium">可以预约同一天的多个服务吗？</h3>
                        <i class="fa fa-chevron-down text-gray-400 transition-transform"></i>
                    </button>
                    <div class="faq-answer hidden mt-2 text-gray-600">
                        是的，您可以预约同一天的多个服务，但请确保各服务之间有足够的时间间隔。为了保证服务质量，建议同一日最多预约两项服务。如需预约更多服务，请提前联系客服人员安排。
                    </div>
                </div>
                
                <div>
                    <button class="flex justify-between items-center w-full text-left" onclick="toggleFAQ(this)">
                        <h3 class="text-lg font-medium">服务不满意可以退款吗？</h3>
                        <i class="fa fa-chevron-down text-gray-400 transition-transform"></i>
                    </button>
                    <div class="faq-answer hidden mt-2 text-gray-600">
                        我们承诺提供优质的服务体验。如您对服务不满意，请在服务结束后24小时内联系客服，并提供详细的反馈。我们会根据具体情况进行评估，并提供相应的解决方案，包括重新服务或部分退款。
                    </div>
                </div>
            </div>
        </div>
    </main>

    <!-- 页脚 -->
    <footer class="bg-dark text-white py-12">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="grid grid-cols-1 md:grid-cols-4 gap-8">
                <div>
                    <h3 class="text-xl font-bold mb-4">服务预约</h3>
                    <p class="text-gray-400 mb-4">为您提供专业、便捷的预约服务体验</p>
                    <div class="flex space-x-4">
                        <a href="#" class="text-gray-400 hover:text-white transition-colors">
                            <i class="fa fa-weixin text-xl"></i>
                        </a>
                        <a href="#" class="text-gray-400 hover:text-white transition-colors">
                            <i class="fa fa-weibo text-xl"></i>
                        </a>
                        <a href="#" class="text-gray-400 hover:text-white transition-colors">
                            <i class="fa fa-qq text-xl"></i>
                        </a>
                    </div>
                </div>
                
                <div>
                    <h3 class="text-lg font-semibold mb-4">快速链接</h3>
                    <ul class="space-y-2">
                        <li><a href="#" class="text-gray-400 hover:text-white transition-colors">首页</a></li>
                        <li><a href="#" class="text-gray-400 hover:text-white transition-colors">服务介绍</a></li>
                        <li><a href="#" class="text-gray-400 hover:text-white transition-colors">预约流程</a></li>
                        <li><a href="#" class="text-gray-400 hover:text-white transition-colors">常见问题</a></li>
                    </ul>
                </div>
                
                <div>
                    <h3 class="text-lg font-semibold mb-4">联系我们</h3>
                    <ul class="space-y-2">
                        <li class="flex items-start">
                            <i class="fa fa-map-marker text-gray-400 mt-1 mr-3"></i>
                            <span class="text-gray-400">北京市朝阳区建国路88号</span>
                        </li>
                        <li class="flex items-start">
                            <i class="fa fa-phone text-gray-400 mt-1 mr-3"></i>
                            <span class="text-gray-400">400-123-4567</span>
                        </li>
                        <li class="flex items-start">
                            <i class="fa fa-envelope text-gray-400 mt-1 mr-3"></i>
                            <span class="text-gray-400">service@example.com</span>
                        </li>
                    </ul>
                </div>
                
                <div>
                    <h3 class="text-lg font-semibold mb-4">营业时间</h3>
                    <ul class="space-y-2">
                        <li class="flex justify-between">
                            <span class="text-gray-400">周一至周五</span>
                            <span class="text-gray-400">9:00 - 18:00</span>
                        </li>
                        <li class="flex justify-between">
                            <span class="text-gray-400">周六</span>
                            <span class="text-gray-400">9:00 - 16:00</span>
                        </li>
                        <li class="flex justify-between">
                            <span class="text-gray-400">周日</span>
                            <span class="text-gray-400">休息</span>
                        </li>
                    </ul>
                </div>
            </div>
            
            <div class="border-t border-gray-800 mt-8 pt-8 text-center text-gray-500">
                <p>© 2025 服务预约系统 版权所有</p>
            </div>
        </div>
    </footer>

    <!-- JavaScript -->
    <script>
        // 全局变量
        let selectedPackage = null;
        let selectedDate = null;
        let selectedTime = null;
        
        // DOM 加载完成后执行
        document.addEventListener('DOMContentLoaded', function() {
            // 初始化日期选择器
            initDateSelector();
            
            // 初始化事件监听
            initEventListeners();
        });
        
        // 初始化日期选择器
        function initDateSelector() {
            const dateSelection = document.getElementById('date-selection');
            const today = new Date();
            
            // 生成未来14天的日期选择
            for (let i = 0; i < 14; i++) {
                const date = new Date(today);
                date.setDate(today.getDate() + i);
                
                const dayName = ['周日', '周一', '周二', '周三', '周四', '周五', '周六'][date.getDay()];
                const dateStr = `${date.getFullYear()}-${String(date.getMonth() + 1).padStart(2, '0')}-${String(date.getDate()).padStart(2, '0')}`;
                const displayStr = `${date.getMonth() + 1}月${date.getDate()}日`;
                
                const dayItem = document.createElement('div');
                dayItem.className = 'day-item';
                dayItem.dataset.date = dateStr;
                dayItem.innerHTML = `
                    <div class="text-sm text-gray-500">${dayName}</div>
                    <div class="font-medium">${displayStr}</div>
                `;
                
                dayItem.addEventListener('click', function() {
                    // 移除其他日期的选中状态
                    document.querySelectorAll('.day-item.date-selected').forEach(item => {
                        item.classList.remove('date-selected');
                    });
                    
                    // 添加当前日期的选中状态
                    this.classList.add('date-selected');
                    selectedDate = this.dataset.date;
                    
                    // 生成对应的时间段
                    generateTimeSlots(dateStr);
                });
                
                dateSelection.appendChild(dayItem);
            }
            
            // 默认选中今天
            if (dateSelection.firstChild) {
                dateSelection.firstChild.click();
            }
        }
        
        // 生成时间段
        function generateTimeSlots(dateStr) {
            const timeSelection = document.getElementById('time-selection');
            timeSelection.innerHTML = '';
            
            // 检查是否是周末
            const date = new Date(dateStr);
            const isWeekend = date.getDay() === 0 || date.getDay() === 6;
            
            // 工作日时间段：9:00-18:00，每30分钟一个时间段
            // 周末时间段：9:00-16:00，每30分钟一个时间段
            const endHour = isWeekend ? 16 : 18;
            
            for (let hour = 9; hour < endHour; hour++) {
                for (let minute = 0; minute < 60; minute += 30) {
                    const startTime = `${hour.toString().padStart(2, '0')}:${minute.toString().padStart(2, '0')}`;
                    const endTime = `${(hour + (minute === 30 ? 1 : 0)).toString().padStart(2, '0')}:${(minute === 30 ? 0 : 30).toString().padStart(2, '0')}`;
                    
                    const timeItem = document.createElement('div');
                    timeItem.className = 'time-slot-item';
                    timeItem.dataset.time = `${startTime}-${endTime}`;
                    timeItem.textContent = `${startTime} - ${endTime}`;
                    
                    timeItem.addEventListener('click', function() {
                        // 移除其他时间的选中状态
                        document.querySelectorAll('.time-slot-item.time-selected').forEach(item => {
                            item.classList.remove('time-selected');
                        });
                        
                        // 添加当前时间的选中状态
                        this.classList.add('time-selected');
                        selectedTime = this.dataset.time;
                    });
                    
                    timeSelection.appendChild(timeItem);
                }
            }
            
            // 默认选中第一个时间段
            if (timeSelection.firstChild) {
                timeSelection.firstChild.click();
            }
        }
        
        // 初始化事件监听
        function initEventListeners() {
            // 套餐选择
            document.querySelectorAll('.package-card').forEach(card => {
                card.addEventListener('click', function() {
                    // 移除其他套餐的选中状态
                    document.querySelectorAll('.package-card.selected').forEach(item => {
                        item.classList.remove('selected');
                    });
                    
                    // 添加当前套餐的选中状态
                    this.classList.add('selected');
                    selectedPackage = {
                        name: this.querySelector('h3').textContent,
                        price: this.dataset.price
                    };
                });
            });
            
            // 下一步到步骤2
            document.getElementById('next-to-step-2').addEventListener('click', function() {
                if (!selectedPackage) {
                    alert('请选择一个套餐');
                    return;
                }
                
                // 更新进度条和步骤指示器
                document.getElementById('progress-bar').style.right = '0';
                document.getElementById('step-2-icon').className = 'w-10 h-10 rounded-full bg-primary text-white flex items-center justify-center transition-all duration-300';
                document.getElementById('step-2-text').className = 'mt-2 text-sm font-medium text-primary transition-all duration-300';
                
                // 隐藏步骤1，显示步骤2
                document.getElementById('step-1-content').classList.add('hidden');
                document.getElementById('step-2-content').classList.remove('hidden');
            });
            
            // 返回步骤1
            document.getElementById('back-to-step-1').addEventListener('click', function() {
                // 更新进度条和步骤指示器
                document.getElementById('progress-bar').style.right = '50%';
                document.getElementById('step-2-icon').className = 'w-10 h-10 rounded-full bg-gray-200 text-gray-500 flex items-center justify-center transition-all duration-300';
                document.getElementById('step-2-text').className = 'mt-2 text-sm font-medium text-gray-500 transition-all duration-300';
                
                // 隐藏步骤2，显示步骤1
                document.getElementById('step-2-content').classList.add('hidden');
                document.getElementById('step-1-content').classList.remove('hidden');
            });
            
            // 下一步到步骤3
            document.getElementById('next-to-step-3').addEventListener('click', function() {
                const name = document.getElementById('fullname').value.trim();
                const phone = document.getElementById('phone').value.trim();
                
                let isValid = true;
                
                // 验证姓名
                if (!name) {
                    document.querySelector('#fullname + .error-message').classList.remove('hidden');
                    isValid = false;
                } else {
                    document.querySelector('#fullname + .error-message').classList.add('hidden');
                }
                
                // 验证手机号
                const phoneRegex = /^1[3-9]\d{9}$/;
                if (!phoneRegex.test(phone)) {
                    document.querySelector('#phone + .error-message').classList.remove('hidden');
                    isValid = false;
                } else {
                    document.querySelector('#phone + .error-message').classList.add('hidden');
                }
                
                if (!isValid) return;
                
                // 更新进度条和步骤指示器
                document.getElementById('progress-bar').style.right = '50%';
                document.getElementById('step-3-icon').className = 'w-10 h-10 rounded-full bg-primary text-white flex items-center justify-center transition-all duration-300';
                document.getElementById('step-3-text').className = 'mt-2 text-sm font-medium text-primary transition-all duration-300';
                
                // 更新确认信息
                document.getElementById('summary-package').textContent = selectedPackage.name;
                document.getElementById('summary-price').textContent = `¥${selectedPackage.price}`;
                
                const formattedDate = new Date(selectedDate).toLocaleDateString('zh-CN', {
                    year: 'numeric',
                    month: 'long',
                    day: 'numeric'
                });
                document.getElementById('summary-date').textContent = formattedDate;
                
                const timeParts = selectedTime.split('-');
                document.getElementById('summary-time').textContent = `${timeParts[0]} - ${timeParts[1]}`;
                
                document.getElementById('summary-name').textContent = name;
                document.getElementById('summary-phone').textContent = phone;
                
                // 隐藏步骤2，显示步骤3
                document.getElementById('step-2-content').classList.add('hidden');
                document.getElementById('step-3-content').classList.remove('hidden');
            });
            
            // 返回步骤2
            document.getElementById('back-to-step-2').addEventListener('click', function() {
                // 更新进度条和步骤指示器
                document.getElementById('progress-bar').style.right = '0';
                document.getElementById('step-3-icon').className = 'w-10 h-10 rounded-full bg-gray-200 text-gray-500 flex items-center justify-center transition-all duration-300';
                document.getElementById('step-3-text').className = 'mt-2 text-sm font-medium text-gray-500 transition-all duration-300';
                
                // 隐藏步骤3，显示步骤2
                document.getElementById('step-3-content').classList.add('hidden');
                document.getElementById('step-2-content').classList.remove('hidden');
            });
            
            // 提交预约
            document.getElementById('submit-booking').addEventListener('click', function() {
                const agreement = document.getElementById('agreement').checked;
                
                if (!agreement) {
                    document.getElementById('agreement-error').classList.remove('hidden');
                    return;
                }
                
                document.getElementById('agreement-error').classList.add('hidden');
                
                // 生成随机预约号
                const bookingId = 'SB' + new Date().getFullYear() + 
                                 String(new Date().getMonth() + 1).padStart(2, '0') + 
                                 String(new Date().getDate()).padStart(2, '0') + 
                                 Math.floor(Math.random() * 1000).toString().padStart(3, '0');
                
                // 更新成功信息
                document.getElementById('booking-id').textContent = bookingId;
                document.getElementById('success-package').textContent = selectedPackage.name;
                document.getElementById('success-price').textContent = `¥${selectedPackage.price}`;
                
                const formattedDate = new Date(selectedDate).toLocaleDateString('zh-CN', {
                    year: 'numeric',
                    month: 'long',
                    day: 'numeric'
                });
                const timeParts = selectedTime.split('-');
                document.getElementById('success-time').textContent = `${formattedDate} ${timeParts[0]}`;
                
                // 隐藏步骤3，显示成功页面
                document.getElementById('step-3-content').classList.add('hidden');
                document.getElementById('booking-success').classList.remove('hidden');
            });
            
            // 新建预约
            document.getElementById('new-booking').addEventListener('click', function() {
                // 重置所有选择
                selectedPackage = null;
                selectedDate = null;
                selectedTime = null;
                
                // 移除所有选中状态
                document.querySelectorAll('.package-card.selected').forEach(item => {
                    item.classList.remove('selected');
                });
                
                document.querySelectorAll('.day-item.date-selected').forEach(item => {
                    item.classList.remove('date-selected');
                });
                
                document.querySelectorAll('.time-slot-item.time-selected').forEach(item => {
                    item.classList.remove('time-selected');
                });
                
                // 清空表单
                document.getElementById('fullname').value = '';
                document.getElementById('phone').value = '';
                document.getElementById('email').value = '';
                document.getElementById('notes').value = '';
                document.getElementById('agreement').checked = false;
                
                // 重置进度条和步骤指示器
                document.getElementById('progress-bar').style.right = '100%';
                document.getElementById('step-2-icon').className = 'w-10 h-10 rounded-full bg-gray-200 text-gray-500 flex items-center justify-center transition-all duration-300';
                document.getElementById('step-2-text').className = 'mt-2 text-sm font-medium text-gray-500 transition-all duration-300';
                document.getElementById('step-3-icon').className = 'w-10 h-10 rounded-full bg-gray-200 text-gray-500 flex items-center justify-center transition-all duration-300';
                document.getElementById('step-3-text').className = 'mt-2 text-sm font-medium text-gray-500 transition-all duration-300';
                
                // 隐藏成功页面，显示步骤1
                document.getElementById('booking-success').classList.add('hidden');
                document.getElementById('step-1-content').classList.remove('hidden');
                
                // 重新初始化日期选择器
                initDateSelector();
            });
        }
        
        // 切换FAQ显示/隐藏
        function toggleFAQ(element) {
            const answer = element.nextElementSibling;
            const icon = element.querySelector('i');
            
            if (answer.classList.contains('hidden')) {
                answer.classList.remove('hidden');
                icon.classList.add('rotate-180');
            } else {
                answer.classList.add('hidden');
                icon.classList.remove('rotate-180');
            }
        }
    </script>
</body>
</html>    
