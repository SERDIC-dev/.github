# SERDIC Web Team Development Guide

최종 업데이트: 2025.09.05

작성자: Hoijun Kim

---

## 📑 목차
1. [환경 설정](#환경-설정)
2. [프로젝트 구조](#프로젝트-구조)
3. [Coding Convention and Style](#coding-convention-and-style)
4. [개발 워크플로우](#개발-워크플로우)

---

## ⚙️ 환경 설정

### 시스템 요구사항

| 항목 | 버전 | 비고 |
|------|------|------|
| 운영체제 | Windows 10/11, Ubuntu 20.04/22.04 | WSL2 지원 |
| Node.js | 22.17.0 | LTS 버전 사용 |
| npm | 11.5.1 | Node.js 포함 |
| Yarn | 1.22.22 | 패키지 매니저 |
| MongoDB | 8.0+ | 데이터베이스 |
| VS Code | Latest | 권장 IDE |

### Windows 환경 설정

#### 1. Node.js 설치
```bash
# Node.js 공식 사이트에서 22.17.0 버전 다운로드
# https://nodejs.org/

# 설치 확인
node --version  # v22.17.0
npm --version   # 11.5.1
```

#### 2. Yarn 설치
```bash
# npm을 통한 Yarn 설치
npm install -g yarn@1.22.22

# 설치 확인
yarn --version  # 1.22.22
```

#### 3. MongoDB 설치
```bash
# MongoDB Community Server 8.0 다운로드
# https://www.mongodb.com/try/download/community

# Windows Service로 자동 시작 설정
# MongoDB Compass GUI 도구 함께 설치 권장
```

#### 4. Git 설치
```bash
# Git for Windows 다운로드
# https://git-scm.com/download/win

git --version
```

### Ubuntu 환경 설정

#### 1. 시스템 업데이트
```bash
sudo apt update && sudo apt upgrade -y
```

#### 2. Node.js 설치 (nvm 사용)
```bash
# nvm 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc

# Node.js 22.17.0 설치
nvm install 22.17.0
nvm use 22.17.0
nvm alias default 22.17.0

# 확인
node --version  # v22.17.0
npm --version   # 11.5.1
```

#### 3. Yarn 설치
```bash
# npm을 통한 설치
npm install -g yarn@1.22.22

# 또는 apt를 통한 설치
curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
sudo apt update
sudo apt install yarn

# 설치 확인
yarn --version  # 1.22.22
```

#### 4. MongoDB 설치
```bash
# MongoDB 8.0 설치
wget -qO - https://www.mongodb.org/static/pgp/server-8.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list
sudo apt update
sudo apt install -y mongodb-org

# MongoDB 시작
sudo systemctl start mongod
sudo systemctl enable mongod
sudo systemctl status mongod
```

### 개발 환경 초기 설정

```bash
# 프로젝트 클론
git clone [repository-url]
cd serdic-web

# 의존성 설치
yarn install

# 환경 변수 설정
cp .env.example .env
# .env 파일을 열어 필요한 환경 변수 설정

# 개발 서버 실행
yarn dev

# 프로덕션 빌드
yarn build
```

---

## 📁 프로젝트 구조

```
serdic-web/
├── 📁 .cert/                    # SSL 인증서 (개발용)
├── 📁 client/                   # 클라이언트 애플리케이션
│   ├── 📁 public/              # 정적 파일
│   │   ├── favicon.ico
│   │   └── index.html
│   ├── 📁 src/
│   │   ├── 📁 assets/          # 정적 리소스
│   │   │   ├── 📁 doc/        # 문서 파일
│   │   │   ├── 📁 font/       # 폰트 파일
│   │   │   │   ├── AppleSDGothicNeo/
│   │   │   │   ├── NanumSquare/
│   │   │   │   ├── NotoSansKR/
│   │   │   │   └── Pretendard/
│   │   │   ├── 📁 img/        # 이미지 파일
│   │   │   │   ├── common/    # 공통 이미지
│   │   │   │   ├── cubemap/   # 3D 텍스처
│   │   │   │   ├── edit/      # 편집기 이미지
│   │   │   │   ├── icon/      # 아이콘
│   │   │   │   └── stream/    # 스트리밍 관련
│   │   │   ├── 📁 json/       # JSON 데이터
│   │   │   ├── 📁 sfx/        # 사운드 효과
│   │   │   └── 📁 vid/        # 비디오 파일
│   │   ├── 📁 components/      # React 컴포넌트
│   │   │   ├── 📁 layout/     # 레이아웃 컴포넌트
│   │   │   │   ├── Header/    
│   │   │   │   └── Sidebar/
│   │   │   └── 📁 modal/      # 모달 컴포넌트
│   │   ├── 📁 hooks/          # 커스텀 React Hooks
│   │   ├── 📁 pages/          # 페이지 컴포넌트
│   │   ├── 📁 services/       # API 서비스 로직
│   │   ├── App.jsx            # 메인 앱 컴포넌트
│   │   ├── index.css          # 전역 스타일
│   │   └── main.jsx          # 엔트리 포인트
│   ├── .eslintrc.js          # ESLint 설정
│   ├── package.json          # 클라이언트 의존성
│   └── vite.config.js        # Vite 설정
├── 📁 packages/                # 모노레포 패키지
│   ├── 📁 common/             # 공통 유틸리티
│   │   ├── index.js
│   │   └── package.json
│   └── 📁 server/             # 백엔드 서버
│       ├── 📁 config/         # 설정 파일
│       ├── 📁 controllers/    # 컨트롤러
│       ├── 📁 routes/         # API 라우트
│       ├── server.js         # 서버 엔트리
│       └── package.json
├── 📁 node_modules/           # 의존성 패키지
├── .env                       # 환경 변수
├── .env.example              # 환경 변수 예시
├── .gitignore                # Git 제외 파일
├── package.json              # 루트 패키지 설정
├── yarn.lock                 # Yarn 락 파일
└── README.md                 # 프로젝트 문서
```

### 폴더 구조 설명

#### `/client` - 프론트엔드 애플리케이션
- **public/**: 정적 파일 (빌드 시 그대로 복사)
- **src/assets/**: 미디어 리소스 (이미지, 폰트, 비디오 등)
- **src/components/**: 재사용 가능한 React 컴포넌트
  - `layout/`: 페이지 레이아웃 컴포넌트
  - `modal/`: 모달 및 팝업 컴포넌트
- **src/hooks/**: 커스텀 React Hooks (상태 관리, 사이드 이펙트)
- **src/pages/**: 라우팅 페이지 컴포넌트
- **src/services/**: API 통신 및 비즈니스 로직

#### `/packages` - 모노레포 구조
- **common/**: 프론트엔드와 백엔드에서 공유하는 유틸리티
- **server/**: Express.js 기반 백엔드 서버
  - `config/`: 데이터베이스 설정, 환경 설정
  - `controllers/`: 비즈니스 로직 처리
  - `routes/`: REST API 엔드포인트 정의

---

## 📋 Coding Convention and Style

### React 컴포넌트 구조

```jsx
// components/Button/Button.jsx
import React, { useState, useEffect } from 'react';
import PropTypes from 'prop-types';
import './Button.css';

/**
 * 재사용 가능한 버튼 컴포넌트
 * 
 * @component
 * @param {Object} props
 * @param {string} props.variant - 버튼 스타일 (primary, secondary, danger)
 * @param {string} props.size - 버튼 크기 (small, medium, large)
 * @param {boolean} props.disabled - 비활성화 여부
 * @param {Function} props.onClick - 클릭 핸들러
 * @param {React.ReactNode} props.children - 버튼 내용
 */
const Button = ({ 
  variant = 'primary', 
  size = 'medium', 
  disabled = false,
  onClick,
  children,
  ...rest 
}) => {
  const [isLoading, setIsLoading] = useState(false);

  const handleClick = async (e) => {
    if (disabled || isLoading) return;
    
    setIsLoading(true);
    try {
      await onClick?.(e);
    } finally {
      setIsLoading(false);
    }
  };

  return (
    <button
      className={`btn btn-${variant} btn-${size}`}
      disabled={disabled || isLoading}
      onClick={handleClick}
      {...rest}
    >
      {isLoading ? <span className="spinner" /> : children}
    </button>
  );
};

Button.propTypes = {
  variant: PropTypes.oneOf(['primary', 'secondary', 'danger']),
  size: PropTypes.oneOf(['small', 'medium', 'large']),
  disabled: PropTypes.bool,
  onClick: PropTypes.func,
  children: PropTypes.node.isRequired
};

export default Button;
```

### Custom Hook 패턴

```jsx
// hooks/useApi.js
import { useState, useEffect } from 'react';
import axios from 'axios';

/**
 * API 호출을 위한 커스텀 훅
 * 
 * @param {string} url - API 엔드포인트
 * @param {Object} options - 요청 옵션
 * @returns {Object} { data, loading, error, refetch }
 */
export const useApi = (url, options = {}) => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  const fetchData = async () => {
    setLoading(true);
    setError(null);
    
    try {
      const response = await axios({
        url,
        ...options
      });
      setData(response.data);
    } catch (err) {
      setError(err.message || 'API 요청 실패');
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    fetchData();
  }, [url]);

  return { data, loading, error, refetch: fetchData };
};
```

### API 서비스 패턴

```javascript
// services/api.js
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_URL || 'http://localhost:3000/api';

// Axios 인스턴스 생성
const apiClient = axios.create({
  baseURL: API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json'
  }
});

// 요청 인터셉터
apiClient.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// 응답 인터셉터
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // 인증 실패 처리
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

// API 서비스 함수들
export const userService = {
  login: (credentials) => apiClient.post('/auth/login', credentials),
  register: (userData) => apiClient.post('/auth/register', userData),
  getProfile: () => apiClient.get('/users/profile'),
  updateProfile: (data) => apiClient.put('/users/profile', data)
};

export const dataService = {
  getList: (params) => apiClient.get('/data', { params }),
  getById: (id) => apiClient.get(`/data/${id}`),
  create: (data) => apiClient.post('/data', data),
  update: (id, data) => apiClient.put(`/data/${id}`, data),
  delete: (id) => apiClient.delete(`/data/${id}`)
};
```

### 명명 규칙

```javascript
// 변수명: camelCase
const userName = 'John Doe';
const isAuthenticated = true;
const apiEndpoint = '/api/users';

// 상수: UPPER_SNAKE_CASE
const MAX_FILE_SIZE = 5 * 1024 * 1024; // 5MB
const API_TIMEOUT = 30000;
const DEFAULT_PAGE_SIZE = 20;

// 컴포넌트명: PascalCase
const UserProfile = () => {};
const NavigationBar = () => {};
const DataTableRow = () => {};

// 파일명
// - 컴포넌트: PascalCase.jsx (Button.jsx, UserCard.jsx)
// - 유틸리티: camelCase.js (formatDate.js, apiClient.js)
// - 스타일: camelCase.css 또는 PascalCase.module.css
```

### ESLint 설정

```javascript
// .eslintrc.js
module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true
  },
  extends: [
    'eslint:recommended',
    'plugin:react/recommended',
    'plugin:react-hooks/recommended'
  ],
  parserOptions: {
    ecmaFeatures: {
      jsx: true
    },
    ecmaVersion: 'latest',
    sourceType: 'module'
  },
  plugins: ['react', 'react-hooks'],
  rules: {
    'react/prop-types': 'warn',
    'react/jsx-uses-react': 'off',
    'react/react-in-jsx-scope': 'off',
    'no-unused-vars': 'warn',
    'no-console': 'warn',
    'indent': ['error', 2],
    'quotes': ['error', 'single'],
    'semi': ['error', 'always']
  }
};
```

---

## 🚀 개발 워크플로우

### Git 브랜치 전략

```bash
main           # 프로덕션 배포 브랜치
├── develop    # 개발 통합 브랜치
│   ├── feature/user-auth      # 기능 개발
│   ├── feature/data-table     # 기능 개발
│   └── fix/login-bug          # 버그 수정
└── hotfix/critical-error      # 긴급 수정
```

### 커밋 메시지 규칙

```bash
# 형식: <type>(<scope>): <subject>

feat(auth): 사용자 로그인 기능 구현
fix(ui): 버튼 클릭 이벤트 버그 수정
docs(readme): 설치 가이드 업데이트
style(components): 코드 포맷팅 정리
refactor(api): API 서비스 구조 개선
test(user): 사용자 모듈 테스트 추가
chore(deps): 패키지 업데이트
```

### 개발 스크립트

```json
// package.json
{
  "scripts": {
    "dev": "concurrently \"yarn dev:client\" \"yarn dev:server\"",
    "dev:client": "cd client && vite",
    "dev:server": "cd packages/server && nodemon server.js",
    "build": "yarn build:client && yarn build:server",
    "build:client": "cd client && vite build",
    "build:server": "cd packages/server && node build.js",
    "test": "jest",
    "lint": "eslint . --ext .js,.jsx",
    "lint:fix": "eslint . --ext .js,.jsx --fix",
    "format": "prettier --write \"**/*.{js,jsx,json,css,md}\""
  }
}
```

### 환경 변수 설정

```bash
# .env.example
# 클라이언트 설정
VITE_API_URL=http://localhost:3000/api
VITE_SOCKET_URL=ws://localhost:3000
VITE_PUBLIC_KEY=your_public_key

# 서버 설정
NODE_ENV=development
PORT=3000
MONGODB_URI=mongodb://localhost:27017/serdic
JWT_SECRET=your_jwt_secret
JWT_EXPIRE=7d

# 외부 서비스
AWS_ACCESS_KEY_ID=your_aws_key
AWS_SECRET_ACCESS_KEY=your_aws_secret
AWS_REGION=ap-northeast-2
```

### 디버깅 설정 (VS Code)

```json
// .vscode/launch.json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "chrome",
      "request": "launch",
      "name": "Client: Chrome",
      "url": "http://localhost:5173",
      "webRoot": "${workspaceFolder}/client/src"
    },
    {
      "type": "node",
      "request": "launch",
      "name": "Server: Node.js",
      "program": "${workspaceFolder}/packages/server/server.js",
      "envFile": "${workspaceFolder}/.env"
    }
  ],
  "compounds": [
    {
      "name": "Full Stack",
      "configurations": ["Client: Chrome", "Server: Node.js"]
    }
  ]
}
```

---

## 📊 성능 최적화 가이드

### Vite 최적화 설정

```javascript
// vite.config.js
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import { visualizer } from 'rollup-plugin-visualizer';

export default defineConfig({
  plugins: [
    react(),
    visualizer({ open: true }) // 번들 크기 분석
  ],
  build: {
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'react-router-dom'],
          utils: ['axios', 'date-fns', 'lodash']
        }
      }
    },
    chunkSizeWarningLimit: 1000
  },
  optimizeDeps: {
    include: ['react', 'react-dom']
  }
});
```

### React 성능 최적화

```jsx
// 메모이제이션 사용 예시
import React, { memo, useMemo, useCallback } from 'react';

const ExpensiveComponent = memo(({ data, onUpdate }) => {
  // 비용이 큰 계산은 useMemo로 캐싱
  const processedData = useMemo(() => {
    return data.map(item => ({
      ...item,
      computed: heavyComputation(item)
    }));
  }, [data]);

  // 함수는 useCallback으로 메모이제이션
  const handleClick = useCallback((id) => {
    onUpdate(id);
  }, [onUpdate]);

  return (
    <div>
      {processedData.map(item => (
        <div key={item.id} onClick={() => handleClick(item.id)}>
          {item.computed}
        </div>
      ))}
    </div>
  );
});
```

---

## 🔒 보안 가이드

### 환경 변수 관리
- `.env` 파일은 절대 Git에 커밋하지 않음
- 민감한 정보는 환경 변수로 관리
- 프로덕션 환경변수는 별도 관리

### XSS 방지
- 사용자 입력은 항상 sanitize
- dangerouslySetInnerHTML 사용 자제
- Content Security Policy 헤더 설정

### 인증/인가
- JWT 토큰은 httpOnly 쿠키로 저장
- Refresh Token 구현으로 보안 강화
- API 요청 시 CORS 설정 확인
