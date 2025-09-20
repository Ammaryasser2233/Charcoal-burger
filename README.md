<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام إدارة مطعم الذواقة</title>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@300;400;500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Firebase SDK -->
    <script src="https://www.gstatic.com/firebasejs/9.15.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.15.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.15.0/firebase-auth-compat.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        
        body {
            font-family: 'Tajawal', sans-serif;
            background: linear-gradient(135deg, #1a1a1a 0%, #2d2d2d 100%);
            color: #f5f5f5;
            line-height: 1.6;
            min-height: 100vh;
        }
        
        /* صفحة تسجيل الدخول */
        .login-container {
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }
        
        .login-form {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 40px;
            width: 100%;
            max-width: 400px;
            box-shadow: 0 25px 50px rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .login-form h2 {
            text-align: center;
            margin-bottom: 30px;
            color: #f8d56f;
            font-size: 2rem;
        }
        
        .form-group {
            margin-bottom: 20px;
        }
        
        .form-group label {
            display: block;
            margin-bottom: 8px;
            color: #e0e0e0;
            font-weight: 500;
        }
        
        .form-group input, .form-group select, .form-group textarea {
            width: 100%;
            padding: 12px 15px;
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 10px;
            color: #fff;
            font-size: 1rem;
            transition: all 0.3s ease;
        }
        
        .form-group input:focus, .form-group select:focus, .form-group textarea:focus {
            outline: none;
            border-color: #f8d56f;
            box-shadow: 0 0 0 3px rgba(248, 213, 111, 0.2);
        }
        
        .login-btn, .btn {
            padding: 12px 20px;
            background: linear-gradient(135deg, #f8d56f, #e67e22);
            border: none;
            border-radius: 10px;
            color: #1a1a1a;
            font-size: 1.1rem;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s ease;
        }
        
        .login-btn:hover, .btn:hover {
            transform: translateY(-2px);
            box-shadow: 0 10px 20px rgba(248, 213, 111, 0.3);
        }
        
        .error-message {
            background: rgba(231, 76, 60, 0.2);
            color: #e74c3c;
            padding: 10px;
            border-radius: 8px;
            margin-bottom: 20px;
            text-align: center;
            display: none;
        }
        
        /* لوحة التحكم */
        .admin-container {
            max-width: 1400px;
            margin: 0 auto;
            padding: 20px;
            display: none;
        }
        
        .admin-header {
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
            border-radius: 15px;
            padding: 20px;
            margin-bottom: 30px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            gap: 15px;
        }
        
        .admin-header h1 {
            color: #f8d56f;
            font-size: 2rem;
        }
        
        .admin-actions {
            display: flex;
            gap: 15px;
            flex-wrap: wrap;
        }
        
        .btn-secondary {
            background: rgba(255, 255, 255, 0.1);
            color: #fff;
            border: 1px solid rgba(255, 255, 255, 0.2);
        }
        
        .btn-danger {
            background: linear-gradient(135deg, #e74c3c, #c0392b);
            color: #fff;
        }
        
        /* تبويبات لوحة التحكم */
        .admin-tabs {
            display: flex;
            margin-bottom: 30px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .admin-tab {
            padding: 15px 25px;
            background: none;
            border: none;
            color: #d0d0d0;
            font-size: 1.1rem;
            font-weight: 600;
            cursor: pointer;
            transition: all 0.3s ease;
            position: relative;
        }
        
        .admin-tab.active {
            color: #f8d56f;
        }
        
        .admin-tab.active::after {
            content: '';
            position: absolute;
            bottom: -1px;
            right: 0;
            width: 100%;
            height: 3px;
            background: #f8d56f;
        }
        
        .tab-content {
            display: none;
        }
        
        .tab-content.active {
            display: block;
        }
        
        /* نموذج إضافة/تعديل */
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            z-index: 1000;
            overflow-y: auto;
        }
        
        .modal-content {
            background: rgba(30, 30, 30, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            padding: 30px;
            margin: 50px auto;
            max-width: 600px;
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 25px;
        }
        
        .modal-header h2 {
            color: #f8d56f;
            font-size: 1.8rem;
        }
        
        .close-btn {
            background: none;
            border: none;
            color: #fff;
            font-size: 1.5rem;
            cursor: pointer;
            transition: color 0.3s ease;
        }
        
        .close-btn:hover {
            color: #e74c3c;
        }
        
        .form-row {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-bottom: 20px;
        }
        
        .tags-input {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            padding: 8px;
            background: rgba(255, 255, 255, 0.1);
            border: 1px solid rgba(255, 255, 255, 0.2);
            border-radius: 10px;
            min-height: 45px;
        }
        
        .tag-item {
            background: rgba(248, 213, 111, 0.2);
            color: #f8d56f;
            padding: 5px 12px;
            border-radius: 15px;
            font-size: 0.9rem;
            display: flex;
            align-items: center;
            gap: 5px;
        }
        
        .tag-item button {
            background: none;
            border: none;
            color: #f8d56f;
            cursor: pointer;
            font-size: 1rem;
        }
        
        .tag-input {
            background: none;
            border: none;
            color: #fff;
            outline: none;
            flex: 1;
            min-width: 100px;
        }
        
        /* قائمة الأطباق */
        .menu-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(350px, 1fr));
            gap: 25px;
            margin-bottom: 30px;
        }
        
        .menu-item-card {
            background: rgba(255, 255, 255, 0.05);
            border-radius: 15px;
            overflow: hidden;
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: all 0.3s ease;
        }
        
        .menu-item-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.3);
        }
        
        .item-image {
            height: 200px;
            overflow: hidden;
            position: relative;
        }
        
        .item-image img {
            width: 100%;
            height: 100%;
            object-fit: cover;
        }
        
        .item-content {
            padding: 20px;
        }
        
        .item-header {
            display: flex;
            justify-content: space-between;
            align-items: start;
            margin-bottom: 15px;
        }
        
        .item-info h3 {
            color: #fff;
            font-size: 1.3rem;
            margin-bottom: 5px;
        }
        
        .item-category {
            color: #f8d56f;
            font-size: 0.9rem;
        }
        
        .item-price {
            font-size: 1.2rem;
            font-weight: 700;
            color: #f8d56f;
            background: rgba(248, 213, 111, 0.15);
            padding: 5px 15px;
            border-radius: 20px;
        }
        
        .item-description {
            color: #d0d0d0;
            margin-bottom: 15px;
            line-height: 1.6;
        }
        
        .item-tags {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
            margin-bottom: 15px;
        }
        
        .item-tag {
            background: rgba(255, 255, 255, 0.1);
            color: #f0f0f0;
            padding: 4px 12px;
            border-radius: 15px;
            font-size: 0.85rem;
        }
        
        .item-actions {
            display: flex;
            gap: 10px;
        }
        
        .item-actions .btn {
            flex: 1;
            justify-content: center;
            padding: 8px;
            font-size: 0.9rem;
        }
        
        /* إدارة الأقسام */
        .categories-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        
        .category-card {
            background: rgba(255, 255, 255, 0.05);
            border-radius: 15px;
            padding: 20px;
            border: 1px solid rgba(255, 255, 255, 0.1);
            transition: all 0.3s ease;
        }
        
        .category-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.3);
        }
        
        .category-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }
        
        .category-name {
            color: #fff;
            font-size: 1.2rem;
            font-weight: 700;
        }
        
        .category-actions {
            display: flex;
            gap: 8px;
        }
        
        .category-actions .btn {
            padding: 6px 10px;
            font-size: 0.8rem;
        }
        
        .category-count {
            color: #d0d0d0;
            font-size: 0.9rem;
        }
        
        /* القائمة الرئيسية للزوار */
        .menu-container {
            max-width: 1200px;
            margin: 0 auto;
            background: rgba(255, 255, 255, 0.05);
            backdrop-filter: blur(10px);
            border-radius: 20px;
            overflow: hidden;
            box-shadow: 0 25px 50px rgba(0, 0, 0, 0.5);
            border: 1px solid rgba(255, 255, 255, 0.1);
            display: none;
        }
        
        .menu-header {
            background: linear-gradient(rgba(0, 0, 0, 0.7), rgba(0, 0, 0, 0.7)), url('https://images.unsplash.com/photo-1414235077428-338989a2e8c0?ixlib=rb-4.0.3&auto=format&fit=crop&w=1200&q=80');
            background-size: cover;
            background-position: center;
            padding: 60px 20px;
            text-align: center;
            position: relative;
        }
        
        .admin-link {
            position: absolute;
            top: 20px;
            left: 20px;
            background: rgba(255, 255, 255, 0.1);
            color: #fff;
            padding: 8px 15px;
            border-radius: 8px;
            text-decoration: none;
            font-size: 0.9rem;
            transition: all 0.3s ease;
        }
        
        .admin-link:hover {
            background: rgba(255, 255, 255, 0.2);
        }
        
        .logo {
            font-size: 3.5rem;
            font-weight: 900;
            margin-bottom: 10px;
            color: #f8d56f;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
        }
        
        .tagline {
            font-size: 1.4rem;
            font-weight: 300;
            color: #e0e0e0;
            margin-bottom: 20px;
        }
        
        .menu-intro {
            font-size: 1.1rem;
            max-width: 700px;
            margin: 0 auto;
            color: #d0d0d0;
            line-height: 1.8;
        }
        
        .menu-section {
            padding: 40px 30px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }
        
        .menu-section:last-child {
            border-bottom: none;
        }
        
        .section-title {
            font-size: 2.2rem;
            font-weight: 700;
            margin-bottom: 30px;
            color: #f8d56f;
            text-align: center;
            position: relative;
            padding-bottom: 15px;
        }
        
        .section-title::after {
            content: '';
            position: absolute;
            bottom: 0;
            right: 50%;
            transform: translateX(50%);
            width: 80px;
            height: 3px;
            background: linear-gradient(90deg, transparent, #f8d56f, transparent);
        }
        
        .menu-items {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
            gap: 25px;
        }
        
        .menu-item {
            background: rgba(255, 255, 255, 0.07);
            border-radius: 15px;
            overflow: hidden;
            transition: all 0.3s ease;
            border: 1px solid rgba(255, 255, 255, 0.1);
            position: relative;
        }
        
        .menu-item::before {
            content: '';
            position: absolute;
            top: 0;
            right: 0;
            width: 5px;
            height: 100%;
            background: linear-gradient(to bottom, #f8d56f, #e67e22);
        }
        
        .menu-item:hover {
            transform: translateY(-5px);
            box-shadow: 0 15px 30px rgba(0, 0, 0, 0.3);
            background: rgba(255, 255, 255, 0.1);
        }
        
        .item-image {
            height: 180px;
            overflow: hidden;
            position: relative;
        }
        
        .item-image img {
            width: 100%;
            height: 100%;
            object-fit: cover;
            transition: transform 0.5s ease;
        }
        
        .menu-item:hover .item-image img {
            transform: scale(1.05);
        }
        
        .item-content {
            padding: 20px;
        }
        
        .item-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
        }
        
        .item-name {
            font-size: 1.4rem;
            font-weight: 700;
            color: #ffffff;
        }
        
        .item-price {
            font-size: 1.3rem;
            font-weight: 700;
            color: #f8d56f;
            background: rgba(248, 213, 111, 0.15);
            padding: 5px 15px;
            border-radius: 20px;
        }
        
        .item-description {
            font-size: 1rem;
            color: #d0d0d0;
            margin-bottom: 15px;
            line-height: 1.6;
        }
        
        .item-tags {
            display: flex;
            flex-wrap: wrap;
            gap: 8px;
        }
        
        .tag {
            background: rgba(255, 255, 255, 0.1);
            color: #f0f0f0;
            padding: 4px 12px;
            border-radius: 15px;
            font-size: 0.85rem;
            display: inline-flex;
            align-items: center;
        }
        
        .tag i {
            margin-left: 5px;
            color: #f8d56f;
        }
        
        footer {
            background: rgba(0, 0, 0, 0.4);
            padding: 30px 20px;
            text-align: center;
        }
        
        .contact-info {
            display: flex;
            justify-content: center;
            flex-wrap: wrap;
            gap: 20px;
            margin-bottom: 20px;
        }
        
        .contact-item {
            display: flex;
            align-items: center;
            color: #d0d0d0;
        }
        
        .contact-item i {
            margin-left: 10px;
            color: #f8d56f;
        }
        
        .social-links {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-top: 20px;
        }
        
        .social-links a {
            display: inline-flex;
            align-items: center;
            justify-content: center;
            width: 40px;
            height: 40px;
            background: rgba(255, 255, 255, 0.1);
            color: #f0f0f0;
            border-radius: 50%;
            transition: all 0.3s ease;
        }
        
        .social-links a:hover {
            background: #f8d56f;
            color: #1a1a1a;
            transform: translateY(-3px);
        }
        
        .copyright {
            margin-top: 20px;
            color: #999;
            font-size: 0.9rem;
        }
        
        /* رسائل النظام */
        .notification {
            position: fixed;
            top: 20px;
            right: 20px;
            padding: 15px 20px;
            border-radius: 10px;
            color: #fff;
            font-weight: 500;
            z-index: 2000;
            transform: translateX(400px);
            transition: transform 0.3s ease;
        }
        
        .notification.show {
            transform: translateX(0);
        }
        
        .notification.success {
            background: linear-gradient(135deg, #27ae60, #2ecc71);
        }
        
        .notification.error {
            background: linear-gradient(135deg, #e74c3c, #c0392b);
        }
        
        /* شاشة التحميل */
        .loading-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 3000;
            flex-direction: column;
        }
        
        .loading-spinner {
            width: 50px;
            height: 50px;
            border: 5px solid rgba(248, 213, 111, 0.3);
            border-radius: 50%;
            border-top-color: #f8d56f;
            animation: spin 1s ease-in-out infinite;
            margin-bottom: 20px;
        }
        
        @keyframes spin {
            to { transform: rotate(360deg); }
        }
        
        .loading-text {
            color: #f8d56f;
            font-size: 1.2rem;
        }
        
        @media (max-width: 768px) {
            .form-row {
                grid-template-columns: 1fr;
            }
            
            .admin-header {
                flex-direction: column;
                text-align: center;
            }
            
            .menu-grid {
                grid-template-columns: 1fr;
            }
            
            .categories-grid {
                grid-template-columns: 1fr;
            }
            
            .logo {
                font-size: 2.5rem;
            }
            
            .tagline {
                font-size: 1.1rem;
            }
            
            .menu-intro {
                font-size: 1rem;
            }
            
            .section-title {
                font-size: 1.8rem;
            }
            
            .menu-items {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <!-- صفحة تسجيل الدخول -->
    <div id="loginPage" class="login-container">
        <form class="login-form" id="loginForm">
            <h2><i class="fas fa-utensils"></i> تسجيل الدخول</h2>
            <div class="error-message" id="errorMessage"></div>
            <div class="form-group">
                <label for="username">اسم المستخدم</label>
                <input type="text" id="username" name="username" required>
            </div>
            <div class="form-group">
                <label for="password">كلمة المرور</label>
                <input type="password" id="password" name="password" required>
            </div>
            <button type="submit" class="login-btn">
                <i class="fas fa-sign-in-alt"></i> دخول
            </button>
        </form>
    </div>

    <!-- لوحة التحكم -->
    <div id="adminPanel" class="admin-container">
        <div class="admin-header">
            <h1><i class="fas fa-cog"></i> لوحة التحكم</h1>
            <div class="admin-actions">
                <button class="btn btn-secondary" onclick="viewMenu()">
                    <i class="fas fa-eye"></i> عرض القائمة
                </button>
                <button class="btn btn-danger" onclick="logout()">
                    <i class="fas fa-sign-out-alt"></i> خروج
                </button>
            </div>
        </div>

        <!-- تبويبات لوحة التحكم -->
        <div class="admin-tabs">
            <button class="admin-tab active" onclick="switchTab('items')">
                <i class="fas fa-utensils"></i> الأطباق
            </button>
            <button class="admin-tab" onclick="switchTab('categories')">
                <i class="fas fa-list"></i> الأقسام
            </button>
        </div>

        <!-- محتوى تبويب الأطباق -->
        <div id="itemsTab" class="tab-content active">
            <div class="admin-actions" style="margin-bottom: 20px;">
                <button class="btn" onclick="showAddModal()">
                    <i class="fas fa-plus"></i> إضافة طبق جديد
                </button>
            </div>
            <div class="menu-grid" id="menuGrid">
                <!-- سيتم عرض الأطباق هنا -->
            </div>
        </div>

        <!-- محتوى تبويب الأقسام -->
        <div id="categoriesTab" class="tab-content">
            <div class="admin-actions" style="margin-bottom: 20px;">
                <button class="btn" onclick="showAddCategoryModal()">
                    <i class="fas fa-plus"></i> إضافة قسم جديد
                </button>
            </div>
            <div class="categories-grid" id="categoriesGrid">
                <!-- سيتم عرض الأقسام هنا -->
            </div>
        </div>
    </div>

    <!-- نموذج إضافة/تعديل الطبق -->
    <div id="itemModal" class="modal">
        <div class="modal-content">
            <div class="modal-header">
                <h2 id="modalTitle">إضافة طبق جديد</h2>
                <button class="close-btn" onclick="closeModal()">
                    <i class="fas fa-times"></i>
                </button>
            </div>
            <form id="itemForm">
                <div class="form-row">
                    <div class="form-group">
                        <label for="itemName">اسم الطبق</label>
                        <input type="text" id="itemName" name="itemName" required>
                    </div>
                    <div class="form-group">
                        <label for="itemPrice">السعر (ج.س)</label>
                        <input type="number" id="itemPrice" name="itemPrice" required>
                    </div>
                </div>
                <div class="form-row">
                    <div class="form-group">
                        <label for="itemCategory">القسم</label>
                        <select id="itemCategory" name="itemCategory" required>
                            <option value="">اختر القسم</option>
                            <!-- سيتم عرض الأقسام هنا -->
                        </select>
                    </div>
                    <div class="form-group">
                        <label for="itemImage">رابط الصورة</label>
                        <input type="url" id="itemImage" name="itemImage" placeholder="https://example.com/image.jpg">
                    </div>
                </div>
                <div class="form-group">
                    <label for="itemDescription">الوصف</label>
                    <textarea id="itemDescription" name="itemDescription" required></textarea>
                </div>
                <div class="form-group">
                    <label>العلامات</label>
                    <div class="tags-input" id="tagsInput">
                        <input type="text" class="tag-input" placeholder="أضف علامة ثم اضغط Enter" id="tagInput">
                    </div>
                </div>
                <div style="display: flex; gap: 10px; margin-top: 25px;">
                    <button type="submit" class="btn" style="flex: 1;">
                        <i class="fas fa-save"></i> حفظ
                    </button>
                    <button type="button" class="btn btn-secondary" onclick="closeModal()" style="flex: 1;">
                        <i class="fas fa-times"></i> إلغاء
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- نموذج إضافة/تعديل القسم -->
    <div id="categoryModal" class="modal">
        <div class="modal-content">
            <div class="modal-header">
                <h2 id="categoryModalTitle">إضافة قسم جديد</h2>
                <button class="close-btn" onclick="closeCategoryModal()">
                    <i class="fas fa-times"></i>
                </button>
            </div>
            <form id="categoryForm">
                <div class="form-group">
                    <label for="categoryName">اسم القسم</label>
                    <input type="text" id="categoryName" name="categoryName" required>
                </div>
                <div class="form-group">
                    <label for="categoryIcon">أيقونة القسم (FontAwesome)</label>
                    <input type="text" id="categoryIcon" name="categoryIcon" placeholder="مثال: fas fa-utensils">
                </div>
                <div style="display: flex; gap: 10px; margin-top: 25px;">
                    <button type="submit" class="btn" style="flex: 1;">
                        <i class="fas fa-save"></i> حفظ
                    </button>
                    <button type="button" class="btn btn-secondary" onclick="closeCategoryModal()" style="flex: 1;">
                        <i class="fas fa-times"></i> إلغاء
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- القائمة الرئيسية للزوار -->
    <div id="menuPage" class="menu-container">
        <header class="menu-header">
            <a href="#" class="admin-link" onclick="showLogin()" style="display: none;" id="adminLink">
                <i class="fas fa-user-shield"></i> دخول الإدارة
            </a>
            <h1 class="logo">مطعم الذواقة</h1>
            <p class="tagline">تجربة طعام لا تُنسى</p>
            <p class="menu-intro">نقدم لكم أطباقاً متنوعة من المأكولات العالمية والمحلية بإعداد فريد ومكونات طازجة من مصادر موثوقة</p>
        </header>
        
        <div id="menuSections">
            <!-- سيتم عرض الأقسام هنا -->
        </div>
        
        <footer>
            <div class="contact-info">
                <div class="contact-item">
                    <i class="fas fa-map-marker-alt"></i>
                    <span>شارع النيل، الخرطوم</span>
                </div>
                <div class="contact-item">
                    <i class="fas fa-phone-alt"></i>
                    <span>0123456789</span>
                </div>
                <div class="contact-item">
                    <i class="fas fa-envelope"></i>
                    <span>info@alzawaga.sd</span>
                </div>
            </div>
            
            <div class="social-links">
                <a href="#"><i class="fab fa-facebook-f"></i></a>
                <a href="#"><i class="fab fa-twitter"></i></a>
                <a href="#"><i class="fab fa-instagram"></i></a>
                <a href="#"><i class="fab fa-whatsapp"></i></a>
            </div>
            
            <p class="copyright">© 2023 مطعم الذواقة. جميع الحقوق محفوظة</p>
        </footer>
    </div>

    <!-- شاشة التحميل -->
    <div id="loadingOverlay" class="loading-overlay">
        <div class="loading-spinner"></div>
        <div class="loading-text">جاري التحميل...</div>
    </div>

    <script>
        // إعداد Firebase
        const firebaseConfig = {
            apiKey: "AIzaSyCdDqQXXeT2AVwHP47c6uUe60ZXoj5cc2M",
            authDomain: "addpr-4c2cc.firebaseapp.com",
            databaseURL: "https://addpr-4c2cc-default-rtdb.firebaseio.com",
            projectId: "addpr-4c2cc",
            storageBucket: "addpr-4c2cc.firebasestorage.app",
            messagingSenderId: "520324533363",
            appId: "1:520324533363:web:63889479986ee525d46e06",
            measurementId: "G-439RERX46C"
        };

        // تهيئة Firebase
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        const auth = firebase.auth();

        // بيانات الاعتماد الافتراضية
        const DEFAULT_CREDENTIALS = {
            username: 'admin',
            password: 'admin123'
        };

        // تهيئة البيانات
        let menuItems = [];
        let categories = [];
        let currentEditId = null;
        let currentEditCategoryId = null;
        let currentTags = [];

        // عرض شاشة التحميل
        function showLoading() {
            document.getElementById('loadingOverlay').style.display = 'flex';
        }

        // إخفاء شاشة التحميل
        function hideLoading() {
            document.getElementById('loadingOverlay').style.display = 'none';
        }

        // عرض الصفحة المناسبة
        function checkAuth() {
            const isLoggedIn = localStorage.getItem('isLoggedIn');
            if (isLoggedIn === 'true') {
                showAdminPanel();
                loadCategories();
                loadMenuItems();
            } else {
                showMenuPage();
                loadCategories();
                loadMenuItems();
            }
        }

        // عرض صفحة تسجيل الدخول
        function showLogin() {
            document.getElementById('loginPage').style.display = 'flex';
            document.getElementById('adminPanel').style.display = 'none';
            document.getElementById('menuPage').style.display = 'none';
        }

        // عرض لوحة التحكم
        function showAdminPanel() {
            document.getElementById('loginPage').style.display = 'none';
            document.getElementById('adminPanel').style.display = 'block';
            document.getElementById('menuPage').style.display = 'none';
        }

        // عرض القائمة للزوار
        function showMenuPage() {
            document.getElementById('loginPage').style.display = 'none';
            document.getElementById('adminPanel').style.display = 'none';
            document.getElementById('menuPage').style.display = 'block';
            document.getElementById('adminLink').style.display = 'inline-flex';
        }

        // عرض القائمة في وضع العرض
        function viewMenu() {
            document.getElementById('loginPage').style.display = 'none';
            document.getElementById('adminPanel').style.display = 'none';
            document.getElementById('menuPage').style.display = 'block';
            document.getElementById('adminLink').style.display = 'none';
        }

        // تبديل التبويبات
        function switchTab(tabName) {
            // تحديث التبويبات النشطة
            document.querySelectorAll('.admin-tab').forEach(tab => {
                tab.classList.remove('active');
            });
            event.target.classList.add('active');
            
            // تحديث المحتوى النشط
            document.querySelectorAll('.tab-content').forEach(content => {
                content.classList.remove('active');
            });
            
            if (tabName === 'items') {
                document.getElementById('itemsTab').classList.add('active');
            } else if (tabName === 'categories') {
                document.getElementById('categoriesTab').classList.add('active');
                renderCategories();
            }
        }

        // تسجيل الدخول
        document.getElementById('loginForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            const username = document.getElementById('username').value;
            const password = document.getElementById('password').value;
            const errorMessage = document.getElementById('errorMessage');
            
            if (username === DEFAULT_CREDENTIALS.username && password === DEFAULT_CREDENTIALS.password) {
                localStorage.setItem('isLoggedIn', 'true');
                showAdminPanel();
                showNotification('تم تسجيل الدخول بنجاح', 'success');
                loadCategories();
                loadMenuItems();
            } else {
                errorMessage.textContent = 'اسم المستخدم أو كلمة المرور غير صحيحة';
                errorMessage.style.display = 'block';
            }
        });

        // تسجيل الخروج
        function logout() {
            localStorage.removeItem('isLoggedIn');
            showMenuPage();
            showNotification('تم تسجيل الخروج بنجاح', 'success');
        }

        // عرض نموذج الإضافة
        function showAddModal() {
            currentEditId = null;
            currentTags = [];
            document.getElementById('modalTitle').textContent = 'إضافة طبق جديد';
            document.getElementById('itemForm').reset();
            document.getElementById('tagsInput').innerHTML = '<input type="text" class="tag-input" placeholder="أضف علامة ثم اضغط Enter" id="tagInput">';
            populateCategorySelect();
            document.getElementById('itemModal').style.display = 'block';
            setupTagsInput();
        }

        // عرض نموذج التعديل
        function showEditModal(id) {
            currentEditId = id;
            const item = menuItems.find(item => item.id === id);
            
            document.getElementById('modalTitle').textContent = 'تعديل الطبق';
            document.getElementById('itemName').value = item.name;
            document.getElementById('itemPrice').value = item.price;
            document.getElementById('itemCategory').value = item.categoryId;
            document.getElementById('itemImage').value = item.image;
            document.getElementById('itemDescription').value = item.description;
            
            currentTags = [...item.tags];
            renderTags();
            populateCategorySelect();
            document.getElementById('itemModal').style.display = 'block';
            setupTagsInput();
        }

        // عرض نموذج إضافة قسم
        function showAddCategoryModal() {
            currentEditCategoryId = null;
            document.getElementById('categoryModalTitle').textContent = 'إضافة قسم جديد';
            document.getElementById('categoryForm').reset();
            document.getElementById('categoryModal').style.display = 'block';
        }

        // عرض نموذج تعديل قسم
        function showEditCategoryModal(id) {
            currentEditCategoryId = id;
            const category = categories.find(cat => cat.id === id);
            
            document.getElementById('categoryModalTitle').textContent = 'تعديل القسم';
            document.getElementById('categoryName').value = category.name;
            document.getElementById('categoryIcon').value = category.icon;
            document.getElementById('categoryModal').style.display = 'block';
        }

        // إغلاق النموذج
        function closeModal() {
            document.getElementById('itemModal').style.display = 'none';
            document.getElementById('itemForm').reset();
        }

        // إغلاق نموذج القسم
        function closeCategoryModal() {
            document.getElementById('categoryModal').style.display = 'none';
            document.getElementById('categoryForm').reset();
        }

        // ملء قائمة الأقسام
        function populateCategorySelect() {
            const categorySelect = document.getElementById('itemCategory');
            categorySelect.innerHTML = '<option value="">اختر القسم</option>';
            
            categories.forEach(category => {
                const option = document.createElement('option');
                option.value = category.id;
                option.textContent = category.name;
                categorySelect.appendChild(option);
            });
        }

        // إعداد حقل العلامات
        function setupTagsInput() {
            const tagInput = document.getElementById('tagInput');
            if (tagInput) {
                tagInput.addEventListener('keypress', function(e) {
                    if (e.key === 'Enter') {
                        e.preventDefault();
                        const tag = this.value.trim();
                        if (tag && !currentTags.includes(tag)) {
                            currentTags.push(tag);
                            renderTags();
                            this.value = '';
                        }
                    }
                });
            }
        }

        // عرض العلامات
        function renderTags() {
            const tagsContainer = document.getElementById('tagsInput');
            tagsContainer.innerHTML = '';
            
            currentTags.forEach(tag => {
                const tagElement = document.createElement('span');
                tagElement.className = 'tag-item';
                tagElement.innerHTML = `
                    ${tag}
                    <button type="button" onclick="removeTag('${tag}')">
                        <i class="fas fa-times"></i>
                    </button>
                `;
                tagsContainer.appendChild(tagElement);
            });
            
            const input = document.createElement('input');
            input.type = 'text';
            input.className = 'tag-input';
            input.placeholder = 'أضف علامة ثم اضغط Enter';
            input.id = 'tagInput';
            tagsContainer.appendChild(input);
            
            setupTagsInput();
        }

        // إزالة علامة
        function removeTag(tag) {
            currentTags = currentTags.filter(t => t !== tag);
            renderTags();
        }

        // حفظ الطبق
        document.getElementById('itemForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            showLoading();
            
            const formData = {
                name: document.getElementById('itemName').value,
                price: parseInt(document.getElementById('itemPrice').value),
                categoryId: document.getElementById('itemCategory').value,
                image: document.getElementById('itemImage').value || `https://picsum.photos/seed/${Date.now()}/500/300.jpg`,
                description: document.getElementById('itemDescription').value,
                tags: currentTags,
                createdAt: firebase.firestore.FieldValue.serverTimestamp()
            };
            
            if (currentEditId) {
                // تعديل طبق موجود
                db.collection('menuItems').doc(currentEditId).update(formData)
                    .then(() => {
                        hideLoading();
                        showNotification('تم تعديل الطبق بنجاح', 'success');
                        closeModal();
                        loadMenuItems();
                    })
                    .catch(error => {
                        hideLoading();
                        showNotification('حدث خطأ أثناء تعديل الطبق: ' + error.message, 'error');
                        console.error('Error updating document: ', error);
                    });
            } else {
                // إضافة طبق جديد
                db.collection('menuItems').add(formData)
                    .then(() => {
                        hideLoading();
                        showNotification('تم إضافة الطبق بنجاح', 'success');
                        closeModal();
                        loadMenuItems();
                    })
                    .catch(error => {
                        hideLoading();
                        showNotification('حدث خطأ أثناء إضافة الطبق: ' + error.message, 'error');
                        console.error('Error adding document: ', error);
                    });
            }
        });

        // حفظ القسم
        document.getElementById('categoryForm').addEventListener('submit', function(e) {
            e.preventDefault();
            
            showLoading();
            
            const formData = {
                name: document.getElementById('categoryName').value,
                icon: document.getElementById('categoryIcon').value || 'fas fa-utensils',
                createdAt: firebase.firestore.FieldValue.serverTimestamp()
            };
            
            if (currentEditCategoryId) {
                // تعديل قسم موجود
                db.collection('categories').doc(currentEditCategoryId).update(formData)
                    .then(() => {
                        hideLoading();
                        showNotification('تم تعديل القسم بنجاح', 'success');
                        closeCategoryModal();
                        loadCategories();
                    })
                    .catch(error => {
                        hideLoading();
                        showNotification('حدث خطأ أثناء تعديل القسم: ' + error.message, 'error');
                        console.error('Error updating document: ', error);
                    });
            } else {
                // إضافة قسم جديد
                db.collection('categories').add(formData)
                    .then(() => {
                        hideLoading();
                        showNotification('تم إضافة القسم بنجاح', 'success');
                        closeCategoryModal();
                        loadCategories();
                    })
                    .catch(error => {
                        hideLoading();
                        showNotification('حدث خطأ أثناء إضافة القسم: ' + error.message, 'error');
                        console.error('Error adding document: ', error);
                    });
            }
        });

        // حذف طبق
        function deleteItem(id) {
            if (confirm('هل أنت متأكد من حذف هذا الطبق؟')) {
                showLoading();
                
                db.collection('menuItems').doc(id).delete()
                    .then(() => {
                        hideLoading();
                        showNotification('تم حذف الطبق بنجاح', 'success');
                        loadMenuItems();
                    })
                    .catch(error => {
                        hideLoading();
                        showNotification('حدث خطأ أثناء حذف الطبق: ' + error.message, 'error');
                        console.error('Error removing document: ', error);
                    });
            }
        }

        // حذف قسم
        function deleteCategory(id) {
            if (confirm('هل أنت متأكد من حذف هذا القسم؟ سيتم حذف جميع الأطباق المرتبطة به أيضاً.')) {
                showLoading();
                
                // حذف القسم
                db.collection('categories').doc(id).delete()
                    .then(() => {
                        // حذف الأطباق المرتبطة بالقسم
                        db.collection('menuItems').where('categoryId', '==', id).get()
                            .then(snapshot => {
                                const batch = db.batch();
                                snapshot.forEach(doc => {
                                    batch.delete(doc.ref);
                                });
                                return batch.commit();
                            })
                            .then(() => {
                                hideLoading();
                                showNotification('تم حذف القسم والأطباق المرتبطة به بنجاح', 'success');
                                loadCategories();
                                loadMenuItems();
                            })
                            .catch(error => {
                                hideLoading();
                                showNotification('حدث خطأ أثناء حذف الأطباق المرتبطة: ' + error.message, 'error');
                                console.error('Error deleting related items: ', error);
                            });
                    })
                    .catch(error => {
                        hideLoading();
                        showNotification('حدث خطأ أثناء حذف القسم: ' + error.message, 'error');
                        console.error('Error removing document: ', error);
                    });
            }
        }

        // تحميل الأقسام من Firebase
        function loadCategories() {
            showLoading();
            
            db.collection('categories').orderBy('name').get()
                .then(querySnapshot => {
                    categories = [];
                    querySnapshot.forEach(doc => {
                        categories.push({
                            id: doc.id,
                            ...doc.data()
                        });
                    });
                    
                    hideLoading();
                    
                    // إذا لم تكن هناك أقسام، أضف الأقسام الافتراضية
                    if (categories.length === 0) {
                        addDefaultCategories();
                    } else {
                        // تحديث القائمة في واجهة المستخدم
                        renderCategories();
                        renderMenuSections();
                    }
                })
                .catch(error => {
                    hideLoading();
                    console.error('Error getting categories: ', error);
                    showNotification('حدث خطأ أثناء تحميل الأقسام: ' + error.message, 'error');
                });
        }

        // تحميل الأطباق من Firebase
        function loadMenuItems() {
            showLoading();
            
            db.collection('menuItems').orderBy('name').get()
                .then(querySnapshot => {
                    menuItems = [];
                    querySnapshot.forEach(doc => {
                        menuItems.push({
                            id: doc.id,
                            ...doc.data()
                        });
                    });
                    
                    hideLoading();
                    
                    // تحديث القائمة في واجهة المستخدم
                    renderAdminMenu();
                    renderMenuSections();
                })
                .catch(error => {
                    hideLoading();
                    console.error('Error getting menu items: ', error);
                    showNotification('حدث خطأ أثناء تحميل الأطباق: ' + error.message, 'error');
                });
        }

        // إضافة الأقسام الافتراضية
        function addDefaultCategories() {
            showLoading();
            
            const defaultCategories = [
                { name: 'المقبلات', icon: 'fas fa-cheese' },
                { name: 'الأطباق الرئيسية', icon: 'fas fa-drumstick-bite' },
                { name: 'الحلويات', icon: 'fas fa-ice-cream' },
                { name: 'المشروبات', icon: 'fas fa-glass-whiskey' }
            ];
            
            const batch = db.batch();
            
            defaultCategories.forEach(category => {
                const ref = db.collection('categories').doc();
                batch.set(ref, {
                    name: category.name,
                    icon: category.icon,
                    createdAt: firebase.firestore.FieldValue.serverTimestamp()
                });
            });
            
            batch.commit()
                .then(() => {
                    hideLoading();
                    showNotification('تمت إضافة الأقسام الافتراضية بنجاح', 'success');
                    loadCategories();
                })
                .catch(error => {
                    hideLoading();
                    console.error('Error adding default categories: ', error);
                    showNotification('حدث خطأ أثناء إضافة الأقسام الافتراضية: ' + error.message, 'error');
                });
        }

        // عرض الأطباق في لوحة التحكم
        function renderAdminMenu() {
            const menuGrid = document.getElementById('menuGrid');
            menuGrid.innerHTML = '';
            
            menuItems.forEach(item => {
                const category = categories.find(cat => cat.id === item.categoryId);
                const categoryName = category ? category.name : 'غير محدد';
                
                const card = document.createElement('div');
                card.className = 'menu-item-card';
                card.innerHTML = `
                    <div class="item-image">
                        <img src="${item.image}" alt="${item.name}">
                    </div>
                    <div class="item-content">
                        <div class="item-header">
                            <div class="item-info">
                                <h3>${item.name}</h3>
                                <div class="item-category">${categoryName}</div>
                            </div>
                            <div class="item-price">${item.price} ج.س</div>
                        </div>
                        <p class="item-description">${item.description}</p>
                        <div class="item-tags">
                            ${item.tags.map(tag => `<span class="item-tag">${tag}</span>`).join('')}
                        </div>
                        <div class="item-actions">
                            <button class="btn" onclick="showEditModal('${item.id}')">
                                <i class="fas fa-edit"></i> تعديل
                            </button>
                            <button class="btn btn-danger" onclick="deleteItem('${item.id}')">
                                <i class="fas fa-trash"></i> حذف
                            </button>
                        </div>
                    </div>
                `;
                menuGrid.appendChild(card);
            });
        }

        // عرض الأقسام في لوحة التحكم
        function renderCategories() {
            const categoriesGrid = document.getElementById('categoriesGrid');
            categoriesGrid.innerHTML = '';
            
            categories.forEach(category => {
                const itemCount = menuItems.filter(item => item.categoryId === category.id).length;
                
                const card = document.createElement('div');
                card.className = 'category-card';
                card.innerHTML = `
                    <div class="category-header">
                        <div class="category-name">
                            <i class="${category.icon}"></i> ${category.name}
                        </div>
                        <div class="category-actions">
                            <button class="btn" onclick="showEditCategoryModal('${category.id}')">
                                <i class="fas fa-edit"></i>
                            </button>
                            <button class="btn btn-danger" onclick="deleteCategory('${category.id}')">
                                <i class="fas fa-trash"></i>
                            </button>
                        </div>
                    </div>
                    <div class="category-count">عدد الأطباق: ${itemCount}</div>
                `;
                categoriesGrid.appendChild(card);
            });
        }

        // عرض الأقسام في القائمة الرئيسية
        function renderMenuSections() {
            const menuSections = document.getElementById('menuSections');
            menuSections.innerHTML = '';
            
            categories.forEach(category => {
                const sectionItems = menuItems.filter(item => item.categoryId === category.id);
                
                if (sectionItems.length > 0) {
                    const section = document.createElement('section');
                    section.className = 'menu-section';
                    section.innerHTML = `
                        <h2 class="section-title">
                            <i class="${category.icon}"></i> ${category.name}
                        </h2>
                        <div class="menu-items">
                            ${sectionItems.map(item => `
                                <div class="menu-item">
                                    <div class="item-image">
                                        <img src="${item.image}" alt="${item.name}">
                                    </div>
                                    <div class="item-content">
                                        <div class="item-header">
                                            <h3 class="item-name">${item.name}</h3>
                                            <span class="item-price">${item.price} ج.س</span>
                                        </div>
                                        <p class="item-description">${item.description}</p>
                                        <div class="item-tags">
                                            ${item.tags.map(tag => `
                                                <span class="tag">
                                                    <i class="fas fa-tag"></i>${tag}
                                                </span>
                                            `).join('')}
                                        </div>
                                    </div>
                                </div>
                            `).join('')}
                        </div>
                    `;
                    menuSections.appendChild(section);
                }
            });
        }

        // عرض الإشعارات
        function showNotification(message, type) {
            const notification = document.createElement('div');
            notification.className = `notification ${type}`;
            notification.textContent = message;
            document.body.appendChild(notification);
            
            setTimeout(() => {
                notification.classList.add('show');
            }, 100);
            
            setTimeout(() => {
                notification.classList.remove('show');
                setTimeout(() => {
                    document.body.removeChild(notification);
                }, 300);
            }, 3000);
        }

        // إغلاق النافذة عند النقر خارجها
        window.onclick = function(event) {
            const itemModal = document.getElementById('itemModal');
            const categoryModal = document.getElementById('categoryModal');
            
            if (event.target === itemModal) {
                closeModal();
            }
            
            if (event.target === categoryModal) {
                closeCategoryModal();
            }
        }

        // تسجيل الخروج التلقائي عند إغلاق النافذة
        window.addEventListener('beforeunload', function() {
            if (localStorage.getItem('isLoggedIn') === 'true') {
                localStorage.removeItem('isLoggedIn');
            }
        });

        // تشغيل التطبيق
        checkAuth();
    </script>
</body>
</html>
