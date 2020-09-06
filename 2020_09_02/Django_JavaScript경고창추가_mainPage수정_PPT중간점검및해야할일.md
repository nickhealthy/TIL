# Django_project



* `JavaScript` 이용해서 playlist 안에 있는 중복된 노래 추가할 시 **경고창 띄우기**

* main 페이지 UI 수정 및 view 파일 수정

* PPT 중간점검 - FEEDBACK

* PPT 해야할 일

  

  ### 메인 페이지 view 설정 - UI(html) 생략

  ```django
  # view 파일
  
  def index(request):
      my_song_list = Song.objects.all()
  
      # 전체 플레이 리스트 데이터를 가져옴
      event_all_list = Playlist.objects.all()
      eventlist = []
      # 전체 플레이 리스트 데이터만큼 디테일을 만듬
      for detail_list in event_all_list:
          list_dict = {}
          song_list = []
          # 디테일 리스트에 노래가 비어있으면 무시
          if detail_list.play_list == 'empty':
              continue
          # 디테일 리스트에 노래들이 있으면 노래들을 리스트로 패킹(아직은 키값으로 유지)
          else:
              song_id_list = detail_list.play_list.split(',')
              # 리스트화된 노래들을 하나씩 뿌려줌(song에 있는 키값과 매칭)
              for song_id in song_id_list:
                  song = get_object_or_404(Song, pk=song_id)
                  # song에 있는 노래들을 하나씪 뿌려줘서 개별적으로 나타냄
                  song_list.append(song)
              list_dict['my_playlist'] = detail_list
              list_dict['song_list'] = song_list
              eventlist.append(list_dict)
              eventlist = eventlist[-3:]
      return render(request, 'plist/index.html', {'eventlist': eventlist, 'my_song_list':my_song_list})
  ```

  

* 웹크롤링 후 이미지 없는 노래 파일을 **공백**에서 **이미지 없음.png**로 대체

  > 멜론에서 웹크롤링해서 이미지가 없는 노래는  DB_plist_song_table에서 **default** 값으로 넣고,  (DB 업데이트) 이후 **default** 값을**이미지 없음.png** 파일로 대체하기 (`view.py` 수정 포함)





## *PPT 중간점검 - FEEDBACK*



1. 각자 구현한 기능들 및 툴들 소개에 고유명사 표기법 따르기
2. 우리가 사용한 툴들 정리한 부분에서 로고같은거 표기하기
3. 라이브러리 기능 추가한 이유 설명해주기(기능, 기술 강조 사항)
4. DB 테이블 구조 그림에 설명해주기
5. 중복된 노래 안들어가는거 설명 추가해주기
6. 맨 마지막에 프로젝트하면서 느낀점
7. 잘된거, 잘안된거, 어려웠던거, 시간이 된다면 더 추가하고 싶은것
8. 화면 설명뿐만 아니라 사용된 기술들을 명시해줘라



### PPT 해야할 일

* 나: DB테이블 각 정보 설명, 구현한 기능 내용 설명 및 화면 구성, 느낀점

* 진수: 프로젝트 목적, 사용 라이브러리 설명, 향후 보완 / 추가할 기능 설명, 느낀점

  > 애자일 방식: 시장 분석해서 장점 어필, 라이브러리: 주요기능들 설명

* 예진: 개발 환경 로고 추가, 스크래핑 과정 설명, 구현한 기능 설명, 느낀점