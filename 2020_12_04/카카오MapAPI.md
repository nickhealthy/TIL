# Today I Learned

* Create React & Use Kakao Map API

---



## Create React & Kakao Map API

[참고사이트](https://gingerkang.tistory.com/65)

[카카오 API](http://apis.map.kakao.com/web/guide/)

※ 함수형 컴포넌트를 기준으로 만들었음 - 클래스형 X

1. 카카오 API에 내 애플리케이션 앱 등록
2. 플랫폼 [Web] 사이트 도메인에 내 사이트 도메인 등록
3. Javascript 키 받기
4. API 사이트와 Javascript 키를 내 코드에 등록 및 내 서비스에 알맞게 코딩



`index.html`

```html
<!-- &libraries=services 카카오 API에서 제공해주는 추가 라이브러리 사용-->
<script type="text/javascript" src="//dapi.kakao.com/v2/maps/sdk.js?appkey=발급받은 APP KEY를 넣으시면 됩니다.&libraries=services"></script>
```



`MapContainer.jsx`

* 지도 생성
* 지도에 마커 표시
* `SearchPlace.jsx` 컴포넌트를 통해 검색 기능 추가

```react
import React, { useEffect } from 'react';

const { kakao } = window;

const MapContainer = ({ searchPlace }) => {
    useEffect(() => {
      const container = document.getElementById("myMap");
      const options = {
        center: new kakao.maps.LatLng(33.450701, 126.570667),
        level: 4,
      };

      const map = new kakao.maps.Map(container, options);
      // 장소 검색 객체를 생성
      const ps = new kakao.maps.services.Places();
      // 키워드로 장소를 검색
      ps.keywordSearch(searchPlace, placesSearchCB);
      // 키워드 검색 완료 시 호출되는 콜백함수
      function placesSearchCB(data, status, pagination) {
        if (status === kakao.maps.services.Status.OK) {
          // 검색된 장소 위치를 기준으로 지도 범위를 재설정하기위해
          // LatLngBounds 객체에 좌표를 추가
          let bounds = new kakao.maps.LatLngBounds();
  
          for (let i = 0; i < data.length; i++) {
            displayMarker(data[i]);
            bounds.extend(new kakao.maps.LatLng(data[i].y, data[i].x));
          }
          // 검색된 장소 위치를 기준으로 지도 범위를 재설정
          map.setBounds(bounds);
        }
      }
      // 지도에 마커를 표시하는 함수
      function displayMarker(place) {
        // 마커를 생성하고 지도에 표시
        let marker = new kakao.maps.Marker({
          map: map,
          position: new kakao.maps.LatLng(place.y, place.x),
        });
        let infowindow = new kakao.maps.InfoWindow({zIndex:1});
        // 마커에 클릭이벤트를 등록
        kakao.maps.event.addListener(marker, 'click', function() {
        // 마커를 클릭하면 장소명이 인포윈도우에 표출
        infowindow.setContent('<div style="padding:5px;font-size:12px;">' + place.place_name + '</div>');
        infowindow.open(map, marker);
        });
      } 
    }, [searchPlace]);

    return (
        <div id='myMap' style={{
            width: '500px', 
            height: '500px'
        }}></div>
    );
}

export default MapContainer;
```



`SearchPlace.jsx`

* 검색 기능을 하는 컴포넌트

```react
import React, { useState } from "react";
import MapContainer from "./MapContainer";

const SearchPlace = () => {
  const [inputText, setInputText] = useState("");
  const [place, setPlace] = useState("");

  const onChange = (e) => {
    setInputText(e.target.value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    setPlace(inputText);
    setInputText("");
  };

  return (
    <>
      <form className="inputForm" onSubmit={handleSubmit}>
        <input
          placeholder="Search Place..."
          onChange={onChange}
          value={inputText}
        />
        <button type="submit">검색</button>
      </form>
      <MapContainer searchPlace={place} />
    </>
  );
};

export default SearchPlace;
```



`APP.js`

```react
import React from 'react';
import PropTypes from "prop-types";
import MapContainer from "./components/MapContainer"


function App() {
  return (
    <>
    <h1>TEST</h1>
    <MapContainer></MapContainer>
    </> 
  );
}

export default App;import React from 'react';
import PropTypes from "prop-types";
import MapContainer from "./components/MapContainer"


function App() {
  return (
    <>
    <h1>TEST</h1>
    <MapContainer></MapContainer>
    </> 
  );
}

export default App;
```


