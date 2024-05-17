---
title: "iOS에서 Apple Maps를 사용하여 배송 앱 실시간 추적 구현"
description: ""
coverImage: "/assets/img/2024-05-17-DeliveryapplivetrackingimplementationusingAppleMapsiniOS_0.png"
date: 2024-05-17 17:57
ogImage: 
  url: /assets/img/2024-05-17-DeliveryapplivetrackingimplementationusingAppleMapsiniOS_0.png
tag: Tech
originalTitle: "Delivery app live tracking implementation using Apple Maps in iOS"
link: "https://medium.com/@vsofficialmail/delivery-app-live-tracking-implementation-using-apple-maps-in-ios-601b4c508922"
---


UIKit 및 MapKit을 사용하여 Apple 지도에서 라이브 추적 구현을 성취했습니다.

[GitHub Repo](https://github.com/VSofficial/Zomato-Live-tracking-Clone-iOS-)

[YouTube](https://www.youtube.com/shorts/Qzi_vZw4p4Q)

<div class="content-ad"></div>

지도에서 MKAnnoatation을 사용하여 사용자 정의 주석(맵 마커)을 정의하는 것으로 시작해 봅시다.

```js
class CustomAnnotation: NSObject, MKAnnotation {
    var coordinate: CLLocationCoordinate2D
    var title: String?
    private let pathCoordinates: [CLLocationCoordinate2D]
    
    init(coordinates: [CLLocationCoordinate2D], title: String?) {
        self.coordinate = coordinates.first ?? CLLocationCoordinate2D(latitude: 0, longitude: 0)
        self.title = title
        self.pathCoordinates = coordinates
        super.init()
    }
}
```

그런 다음 맵에서 마커의 사용자 정의 뷰(심볼)를 생성합니다.

```js
// 지도의 주석용 사용자 정의 뷰
public class CustomAnnotationView: MKAnnotationView {
    override init(annotation: MKAnnotation?, reuseIdentifier: String?) {
        super.init(annotation: annotation, reuseIdentifier: reuseIdentifier)
        self.image = UIImage(named: "delivery")
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

<div class="content-ad"></div>

이제 우리가 MKMapViewDelegate를 사용하여 맵에 사용자 정의 주석을 구현하는 부분으로 넘어가 봅시다.

```swift
extension ViewController: MKMapViewDelegate {
    
    func mapView(_ mapView: MKMapView, viewFor annotation: MKAnnotation) -> MKAnnotationView? {
        guard let annotation = annotation as? CustomAnnotation else {
            return nil
        }
        let identifier = "CustomAnnotationView"
        var annotationView = mapView.dequeueReusableAnnotationView(withIdentifier: identifier) as? MKPinAnnotationView
        
        if annotationView == nil {
            annotationView = MKPinAnnotationView(annotation: annotation, reuseIdentifier: identifier)
            annotationView?.canShowCallout = true
        } else {
            annotationView?.annotation = annotation
        }
        return annotationView
    }
}
```

다음으로 리다라를 따라가며

```swift
extension ViewController {
    func mapView(_ mapView: MKMapView, rendererFor overlay: MKOverlay) -> MKOverlayRenderer {
        if overlay is MKPolyline {
            let renderer = MKPolylineRenderer(overlay: overlay)
            renderer.strokeColor = UIColor.systemTeal
            renderer.lineWidth = 6
            return renderer
        }
        return MKOverlayRenderer(overlay: overlay)
    }
}
```

<div class="content-ad"></div>

위의 코드는 지도에 이동 (배송) 경로를 포함하는 스트로크 라인을 보여주는 데 도움이 됩니다.

이제, 주요 기능이 구현된 섹션으로 이동해 보겠습니다.

```js
// MapKit에서 지도의 주요 구성
    private func configureMap() {
        mapView.delegate = self
        
        let pathCoordinates = [
            CLLocationCoordinate2D(latitude: Double(startLatitude)!, longitude: Double(startLongitude)!),
            CLLocationCoordinate2D(latitude: Double(endLatitude)!, longitude: Double(endLongitude)!),
        ]
        
        let annotation = CustomAnnotation(coordinates: pathCoordinates, title: "Moving Pins")
        mapView.addAnnotation(annotation)
        
        let regionRadius: CLLocationDistance = 350
        let region = MKCoordinateRegion(center: pathCoordinates.first!, latitudinalMeters: regionRadius, longitudinalMeters: regionRadius)
        
        let request = MKDirections.Request()
        request.source = MKMapItem(placemark: MKPlacemark(coordinate: pathCoordinates[0]))
        request.destination = MKMapItem(placemark: MKPlacemark(coordinate: pathCoordinates[1]))
        request.transportType = .automobile
        
        let directions = MKDirections(request: request)
        directions.calculate { (response, error) in
            guard let route = response?.routes.first else {
                if let error = error {
                    print("방향을 가져오는 중 에러 발생: \(error.localizedDescription)")
                }
                return
            }
            self.mapView.addOverlay(route.polyline)
            self.addPinAndFollowRoute(route: route, duration: TimeInterval(self.timedVariable))
        }
        mapView.setRegion(region, animated: true)
    }
    
    // 배달원용 경로 따르는 알고리즘
    func addPinAndFollowRoute(route: MKRoute, duration: TimeInterval) {
        let pin = MKPointAnnotation()
        pin.coordinate = route.polyline.coordinate
        mapView.addAnnotation(pin)
        
        var elapsedTime: TimeInterval = 0.0
        let totalDuration = duration
        let pointCount = route.polyline.pointCount
        
        Timer.scheduledTimer(withTimeInterval: 0.01, repeats: true) { timer in
            elapsedTime += 0.01
            
            if elapsedTime >= totalDuration {
                self.showAlert()
                self.ordertitle.text = "주문 배달 완료!! 🎉🎉 "
                timer.invalidate()
                return
            }
            
            let fraction = elapsedTime / totalDuration
            let index = Int(fraction * Double(pointCount - 1))
            
            if index < pointCount - 1 {
                let startCoordinate = route.polyline.points()[index].coordinate
                let endCoordinate = route.polyline.points()[index + 1].coordinate
                let interpolatedCoordinate = self.interpolateCoordinate(startCoordinate, endCoordinate, fraction)
                
                UIView.animate(withDuration: 0.01) { // 부드러운 이동을 위한 애니메이션 시간 감소
                    pin.coordinate = interpolatedCoordinate
                }
            }
        }
    }

    func interpolateCoordinate(_ start: CLLocationCoordinate2D, _ end: CLLocationCoordinate2D, _ fraction: Double) -> CLLocationCoordinate2D {
        let lat = start.latitude + (end.latitude - start.latitude) * fraction
        let lon = start.longitude + (end.longitude - start.longitude) * fraction
        return CLLocationCoordinate2D(latitude: lat, longitude: lon)
    }
    
    // 주문 배달 메시지
    func showAlert() {
          let alert = UIAlertController(title: "주문 배달 완료", message: "식사를 즐기세요", preferredStyle: .alert)
          let okAction = UIAlertAction(title: "확인", style: .default) { _ in
              // 필요하다면 확인 작업 처리
          }
          alert.addAction(okAction)
          present(alert, animated: true, completion: nil)
      }

}
```

pathCoordinates는 시작 및 끝 위치의 좌표를 정의합니다.

<div class="content-ad"></div>

```swift
region 변수는 우리의 배송 위치가 위치한 지역만 표시하는 데 사용됩니다.

func addPinAndFollowRoute(route: MKRoute, duration: TimeInterval)

위의 함수는 경로를 따라가는 데 사용됩니다. 여행을 완료하는 데 필요한 시간과 경로를 정의할 수 있습니다.

var errorMessage: String?
var startLatitude: String = "0"
var startLongitude: String = "0.0"
var endLatitude: String = "0.0"
var endLongitude: String = "0.0"
var timedVariable: Int = 1
```

<div class="content-ad"></div>

위 변수들은 ViewController에 정의되어 있으며 하드 코딩된 값이나 API 응답에서 가져온 값으로 수정할 수 있습니다.

configureMap() 내에서 route의 정의 (이미 위 코드에 포함되어 있음)

```js
guard let route = response?.routes.first else {
                if let error = error {
                    print("Error getting directions: \(error.localizedDescription)")
                }
                return
            }
```

그리고 여기서 우리 애플리케이션이 완료됩니다.

<div class="content-ad"></div>

GitHub 저장소 및 YouTube 비디오를 첨부했어요. 코드베이스와 애플리케이션 데모를 확인해보세요!