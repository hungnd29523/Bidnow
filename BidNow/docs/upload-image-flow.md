# Luồng Upload Ảnh - BidNow

## Tổng quan
Hệ thống BidNow hỗ trợ upload ảnh đại diện (avatar) cho người dùng. Luồng upload được thực hiện qua API endpoint `/api/Users/{userId}/avatar`.

## Các Trường Hợp Sử Dụng

### 1. Upload Avatar trong User Settings
**File:** `components/user-settings.tsx`

### 2. Upload Avatar khi Tạo User mới (Admin)
**File:** `components/admin/create-user-dialog.tsx`

### 3. Upload Avatar khi Chỉnh sửa User (Admin)
**File:** `components/admin/edit-user-dialog.tsx`

### 4. Upload Avatar trong User Profile
**File:** `components/user-profile.tsx`

---

## Luồng Chi Tiết

### 📋 Bước 1: Người dùng chọn file ảnh

```
User clicks "Tải ảnh đại diện" button
    ↓
File input dialog opens
    ↓
User selects image file
    ↓
onChange event triggered
```

**Code Location:**
- `components/user-settings.tsx` (line 119-166)
- `components/admin/create-user-dialog.tsx` (line 86-105)

**Validation tại Frontend:**
```typescript
// Kiểm tra loại file
if (!file.type.startsWith("image/")) {
  // Hiển thị lỗi: "Vui lòng chọn file ảnh"
  return
}

// Kiểm tra kích thước file (tối đa 10MB)
if (file.size > 10 * 1024 * 1024) {
  // Hiển thị lỗi: "Kích thước file không được vượt quá 10MB"
  return
}
```

---

### 📋 Bước 2: Preview ảnh (tùy chọn)

```
File selected successfully
    ↓
Create preview URL: URL.createObjectURL(file)
    ↓
Display preview in UI
    ↓
Store file in state: setAvatarFile(file)
```

**Code Location:**
- `components/admin/create-user-dialog.tsx` (line 102-104)
- `components/user-profile.tsx` (similar pattern)

---

### 📋 Bước 3: Gửi request upload lên server

**API Method:** `UsersAPI.uploadAvatar(userId, file)`

**File:** `lib/api/users.ts` (line 313-327)

```typescript
static async uploadAvatar(id: number, file: File): Promise<{ avatarUrl: string }> {
  // 1. Tạo FormData
  const formData = new FormData();
  formData.append('file', file);

  // 2. Gửi POST request
  const response = await fetch(
    `${API_BASE}/api/Users/${id}/avatar`, 
    {
      method: 'POST',
      body: formData,  // Không cần set Content-Type header, browser tự động set với boundary
    }
  );

  // 3. Xử lý response
  return await this.handleResponse<{ avatarUrl: string }>(response);
}
```

**Endpoint:** `POST /api/Users/{userId}/avatar`

**Request Format:**
- Method: `POST`
- URL: `{API_BASE}/api/Users/{userId}/avatar`
- Body: `FormData` với field `file`
- Headers: Browser tự động set `Content-Type: multipart/form-data; boundary=...`

**Response Format:**
```json
{
  "avatarUrl": "path/to/uploaded/image.jpg"
}
```

---

### 📋 Bước 4: Xử lý Response

**Success Case:**
```
Server returns 200 OK với avatarUrl
    ↓
Update local state: setAvatarUrl(result.avatarUrl)
    ↓
Refresh user data: await refreshUser()
    ↓
Show success toast: "Cập nhật avatar thành công"
    ↓
Reset file input: e.target.value = ""
```

**Error Case:**
```
Server returns error (4xx/5xx)
    ↓
handleResponse throws Error với message
    ↓
Catch error trong try-catch
    ↓
Show error toast: error.message || "Không thể tải lên avatar"
    ↓
Set loading state: setIsUploadingAvatar(false)
```

**Code Location:**
- `components/user-settings.tsx` (line 143-165)

---

## Sơ Đồ Luồng Tổng Quan

```
┌─────────────────────────────────────────────────────────────┐
│                    USER INTERFACE                            │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  User clicks "Tải ảnh đại diện" button               │   │
│  └──────────────────┬───────────────────────────────────┘   │
│                     │                                        │
│  ┌──────────────────▼───────────────────────────────────┐   │
│  │  File Input Dialog Opens                             │   │
│  └──────────────────┬───────────────────────────────────┘   │
│                     │                                        │
│  ┌──────────────────▼───────────────────────────────────┐   │
│  │  User selects image file                             │   │
│  └──────────────────┬───────────────────────────────────┘   │
└─────────────────────┼───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│              FRONTEND VALIDATION                            │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  ✓ Check file type (must be image/*)                 │   │
│  │  ✓ Check file size (max 10MB)                        │   │
│  └──────────────────┬───────────────────────────────────┘   │
│                     │                                        │
│  ┌──────────────────▼───────────────────────────────────┐   │
│  │  Create preview: URL.createObjectURL(file)            │   │
│  │  Store file in state                                 │   │
│  └──────────────────┬───────────────────────────────────┘   │
└─────────────────────┼───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│              API CALL (UsersAPI.uploadAvatar)              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  1. Create FormData                                  │   │
│  │     formData.append('file', file)                    │   │
│  │                                                       │   │
│  │  2. POST /api/Users/{userId}/avatar                  │   │
│  │     Body: FormData                                   │   │
│  └──────────────────┬───────────────────────────────────┘   │
└─────────────────────┼───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│                    BACKEND API                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  POST /api/Users/{userId}/avatar                       │   │
│  │  - Receive FormData                                    │   │
│  │  - Validate file                                       │   │
│  │  - Save file to storage                                │   │
│  │  - Update user.avatarUrl in database                   │   │
│  │  - Return { avatarUrl: "..." }                        │   │
│  └──────────────────┬───────────────────────────────────┘   │
└─────────────────────┼───────────────────────────────────────┘
                      │
                      ▼
┌─────────────────────────────────────────────────────────────┐
│              RESPONSE HANDLING                              │
│  ┌──────────────────────────────────────────────────────┐   │
│  │  Success:                                             │   │
│  │  - Update avatarUrl state                             │   │
│  │  - Refresh user data                                   │   │
│  │  - Show success toast                                 │   │
│  │                                                       │   │
│  │  Error:                                               │   │
│  │  - Show error toast                                   │   │
│  │  - Reset loading state                                │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## Chi Tiết Implementation

### 1. User Settings Upload (`components/user-settings.tsx`)

```typescript
// Line 119-166
const handleAvatarUpload = async (e: React.ChangeEvent<HTMLInputElement>) => {
  const file = e.target.files?.[0]
  if (!file || !user?.id) return

  // Validation
  if (!file.type.startsWith("image/")) {
    toast({ title: "Lỗi", description: "Vui lòng chọn file ảnh", variant: "destructive" })
    return
  }

  if (file.size > 10 * 1024 * 1024) {
    toast({ title: "Lỗi", description: "Kích thước file không được vượt quá 10MB", variant: "destructive" })
    return
  }

  setIsUploadingAvatar(true)
  try {
    const result = await UsersAPI.uploadAvatar(user.id, file)
    setAvatarUrl(result.avatarUrl)
    
    toast({ title: "Thành công", description: "Cập nhật avatar thành công" })
    await refreshUser?.()
  } catch (error: any) {
    toast({ title: "Lỗi", description: error.message || "Không thể tải lên avatar", variant: "destructive" })
  } finally {
    setIsUploadingAvatar(false)
    e.target.value = "" // Reset input
  }
}
```

### 2. Create User Dialog Upload (`components/admin/create-user-dialog.tsx`)

```typescript
// Line 119-137
const uploadAvatarFile = async (file: File, userId: number): Promise<string> => {
  const formData = new FormData()
  formData.append("file", file)

  const API_BASE = process.env.NEXT_PUBLIC_API_BASE || "http://localhost:5167"
  const response = await fetch(`${API_BASE}/api/Users/${userId}/avatar`, {
    method: "POST",
    body: formData,
    credentials: "include",
  })

  if (!response.ok) {
    const error = await response.json().catch(() => ({ message: "Không thể upload avatar" }))
    throw new Error(error.message || "Không thể upload avatar")
  }

  const data = await response.json()
  return data.avatarUrl
}

// Line 149-171: Sử dụng sau khi tạo user
const createdUser = await onSubmit(userData, selectedRole)

// Upload avatar file if selected (after user is created)
if (avatarFile && createdUser?.id) {
  try {
    await uploadAvatarFile(avatarFile, createdUser.id)
  } catch (error: any) {
    console.warn("Failed to upload avatar:", error)
  }
}
```

### 3. API Layer (`lib/api/users.ts`)

```typescript
// Line 313-327
static async uploadAvatar(id: number, file: File): Promise<{ avatarUrl: string }> {
  try {
    const formData = new FormData();
    formData.append('file', file);

    const response = await fetch(`${API_BASE}${API_ENDPOINTS.USERS.UPLOAD_AVATAR(id)}`, {
      method: 'POST',
      body: formData,
    });
    return await this.handleResponse<{ avatarUrl: string }>(response);
  } catch (error) {
    console.error('Upload avatar error:', error);
    throw error;
  }
}
```

**Lưu ý:** `API_ENDPOINTS.USERS.UPLOAD_AVATAR` không được định nghĩa trong `config.ts`, nhưng endpoint thực tế là `/api/Users/${id}/avatar`.

---

## Validation Rules

### Frontend Validation:
1. ✅ File type: Phải là `image/*`
2. ✅ File size: Tối đa 10MB (10 * 1024 * 1024 bytes)
3. ✅ User ID: Phải tồn tại

### Backend Validation (Expected):
1. ✅ File type validation
2. ✅ File size validation
3. ✅ User authentication/authorization
4. ✅ File storage security

---

## Error Handling

### Các Lỗi Có Thể Xảy Ra:

1. **File không hợp lệ:**
   - Message: "Vui lòng chọn file ảnh"
   - Location: Frontend validation

2. **File quá lớn:**
   - Message: "Kích thước file không được vượt quá 10MB"
   - Location: Frontend validation

3. **Upload thất bại:**
   - Message: error.message từ server hoặc "Không thể tải lên avatar"
   - Location: API call error handling

4. **User không tồn tại:**
   - Message: Từ server response
   - Location: Backend validation

---

## State Management

### Loading States:
- `isUploadingAvatar`: Boolean - Trạng thái đang upload
- Hiển thị spinner/loader trong UI khi `true`

### File States:
- `avatarFile`: File | null - File đã chọn
- `avatarPreview`: string | null - URL preview (từ `URL.createObjectURL`)
- `avatarUrl`: string | null - URL ảnh từ server

---

## Best Practices

1. ✅ **Validation trước khi upload:** Kiểm tra file type và size ở frontend
2. ✅ **Preview trước khi upload:** Cho phép user xem ảnh trước khi upload
3. ✅ **Loading state:** Hiển thị trạng thái đang upload
4. ✅ **Error handling:** Xử lý và hiển thị lỗi rõ ràng
5. ✅ **Reset input:** Reset file input sau khi upload thành công/thất bại
6. ✅ **Cleanup:** Revoke object URL để tránh memory leak

---

## API Endpoint Details

**Endpoint:** `POST /api/Users/{userId}/avatar`

**Request:**
- Method: `POST`
- URL: `{API_BASE}/api/Users/{userId}/avatar`
- Body: `multipart/form-data`
  - Field name: `file`
  - Type: `File` (image)

**Response Success (200 OK):**
```json
{
  "avatarUrl": "uploads/avatars/user-123-avatar.jpg"
}
```

**Response Error (4xx/5xx):**
```json
{
  "message": "Error message here"
}
```

---

## Files Liên Quan

1. **Frontend Components:**
   - `components/user-settings.tsx` - Upload trong settings
   - `components/admin/create-user-dialog.tsx` - Upload khi tạo user
   - `components/admin/edit-user-dialog.tsx` - Upload khi edit user
   - `components/user-profile.tsx` - Upload trong profile

2. **API Layer:**
   - `lib/api/users.ts` - API method `uploadAvatar()`
   - `lib/api/config.ts` - API configuration (thiếu UPLOAD_AVATAR endpoint)

3. **Types:**
   - `lib/api/types.ts` - Type definitions

---

## Ghi Chú

- Endpoint `UPLOAD_AVATAR` chưa được định nghĩa trong `API_ENDPOINTS.USERS` trong file `config.ts`
- Hiện tại endpoint được hardcode: `/api/Users/${id}/avatar`
- Nên thêm endpoint vào config để dễ quản lý:
  ```typescript
  UPLOAD_AVATAR: (id: number) => `/api/Users/${id}/avatar`,
  ```

