"vim setting"
syntax on						" 구문강조 사용
set autoindent						" 자동 들여쓰기
set smartindent						" 스마트한 들여쓰기
set cindent						" C 프로그래밍용 자동 들여쓰기
set shiftwidth=4					" 자동 들여쓰기 4칸
set tabstop=4						" 탭을 4칸으로
set nobackup						" 백업 파일을 안만듬
set nowrapscan						" 검색할 때 문서의 끝에서 처음으로 안돌아감
set ignorecase						" 검색시 대소문자 무시, set ic 도 가능
set hlsearch						" 검색어 강조, set hls 도 가능
set number						" 행번호 표시, set nu 도 가능
set nocompatible					" 오리지날 VI와 호환하지 않음
set backspace=eol,start,indent				" 줄의 끝, 시작, 들여쓰기에서 백스페이스시 이전줄로
set ruler						" 화면 우측 하단에 현재 커서의 위치(줄,칸) 표시"
set cursorline						" 편집 위치에 커서 라인 설정
set laststatus=2					" 상태바 표시를 항상한다
set incsearch						" 키워드 입력시 점진적 검색
set fencs=ucs-bom,utf-8,euc-kr.latin1			" 한글 파일은 euc-kr로, 유니코드는 유니코드로
set fileencoding=utf-8					" 파일저장인코딩
set tenc=utf-8						" 터미널 인코딩
set background=dark					" 하이라이팅 lihgt / dark
set history=1000					" vi 편집기록 기억갯수 .viminfo에 기록
set t_Co=256						" 색 조정
highlight Comment term=bold cterm=bold ctermfg=4	" 코멘트 하이라이트
set wrap
set noswapfile
set lbr
"set visualbell						" 키를 잘못눌렀을 때
"화면 프레시
""set mouse=a	
"autocmd BufEnter * if &ft != 'nerdtree' && !empty(expand('%:p')) | NERDTreeFind | endif
nnoremap <C-n> :NERDTreeToggle<CR>
nnoremap <C-f> :NERDTreeFind<CR>
set nocompatible              " be iMproved, required
filetype off                  " required
set wildignore+=*/node_modules/*,*/tmp/*,*/build/*
let g:ctrlp_custom_ignore = '\v[\/]\.(git|hg|svn)$'
let g:ctrlp_map = '<c-p>'
map <C-n> :NERDTreeToggle<CR>
set rtp+=~/.vim/bundle/Vundle.vim


nnoremap <silent> <C-Tab> :tabnext<CR>
nnoremap <silent> <C-S-Tab> :tabprevious<CR>


call vundle#begin()
" 플러그인 설정"
Plugin 'VundleVim/Vundle.vim'
Plugin 'ryanoasis/vim-devicons'
Plugin 'Xuyuanp/nerdtree-git-plugin'
Plugin 'tiagofumo/vim-nerdtree-syntax-highlight'
Plugin 'scrooloose/syntastic'
Plugin 'scrooloose/nerdcommenter'			" 주석
Plugin 'morhetz/gruvbox'
Plugin 'taglist-plus'					" 소스파일의 클래스, 함수, 변수 정보 창
Plugin 'altercation/vim-colors-solarized'
Plugin 'vim-airline/vim-airline'
Plugin 'ctrlpvim/ctrlp.vim'				" 하위 디렉토리 파일 찾기
Plugin 'scrooloose/nerdtree'				" 파일트리
Plugin 'AutoComplPop'					" 자동 완성(Ctrl + P)를 누르지 않음
Plugin 'elzr/vim-json'					" JSON 파일 보기
Plugin 'Quich-Filter'					" 라인 필터링
Plugin 'Lokaltog/vim-easymotion'			" 한 화면에서 커서 이동
Plugin 'vim-airline/vim-airline-themes'
call vundle#end()
syntax enable
set background=dark
colorscheme solarized
filetype plugin indent on




"ctrlp setting"
let g:ctrlp_custom_ignore = {
  \ 'dir':  '\.git$\|public$\|log$\|tmp$\|vendor$',
  \ 'file': '\v\.(exe|so|dll)$'
  \ }









"----------------------------------------------------------------------"
"" gruvbox 설정
"----------------------------------------------------------------------"
set background=dark
let g:gruvbox_contrast_dark = 'soft'
colorscheme gruvbox

