> [스프링5 레시피](http://www.hanbit.co.kr/store/books/look.php?p_code=B3859466837)을 보고 정리한 글입니다.

서블릿 API 초창기 시절에는 구현 컨테이너 대부분이 요청당 스레드 하나만 사용했습니다. **컨테이너가 요청을 받아 처리를 끄탠고 클라이언트 에게 응답을 돌려주기 전까지 스레드는 항상 블로킹 상태였습니다.**

서블릿 3 명세부터 HTTP 요청을 비동기로 처리할 길이 열리면서 최초 HTTP요청을 접수했던 스레드를 해제할 수 있게 되었습니다.

새 스레드는 백그라운드에서 움직이다가 결과가 준비되는 즉시 클라이언트로 보낼 수 있게 되었습니다.

지난 몇년 동안 리액티브 프로그래밍 이 조명받고 있기 시작했고 스프링 5부터는 리액티브 웹 어플리케이션 개발도 가능하게 되었습니다.

최근 HTML 랜더링 작업을 클라이언트에서로 넘어 갔고 JSON 등 다른 표현형을 돌려주는 식으로 통신 방식을 바귀었습니다. XMLHttpRequest 객체를 사용한 클라이언트의 비동기 호출에 의해 통신이 시작되었다는 점은 큰 변화지만 `요청을 하면 응답을 돌려준다`는 전통적인 흐름은 그대로입니다. 

서버 전송 이벤트로 서버 클라이언트로 단방향 통신을 걸수도 있고 웹소켓 프로토콜로 전이준 양방향 통신을 제공하는 흥미로운 기술도 있습니다.


## TaskExecutor로 요청을 비동기 처리하기
HTTP 요청을 동기적으로 처리하면 요청 처리 스레드가 블로킹되고 응답은 열린 상태로 씌어질 준비를 합니다. 일정한 작업 시간이 소요되는 호출이면 이렇게 하는 편이 좋지만 스레드를 블로킹하지 않고 백그라운드에서 처리한 후 결괏값을 유저에게 올려주는 것이 더 효율적입니다.


### 비동기 처리 설정하기
```java
public class CourtWebApplicationInitializer implements WebApplicationInitializer {
    
    @Override
    public void onStartup(ServletContext ctx) throws ServletException {
        DispatcherServlet servlet = new DispatcherServlet();
        ServletRegistration.Dynamic registration = ctx.addServlet("dispatcher", servlet);
        registration.setAsyncSupported(true);
    }
}
```
* 비동기 처리를 사용하려면 모든 필터와 서블릿이 비동기로 동작할 수 있도록 활성화해야합니다.
* 필터/서블릿을 등록할 때 `setAsyncSupported(ture)` 메서드 호출로 비동기 모드를 작동 시킬 수있습니다.
  
```java
@Configuration
public class AsyncConfiguration extends WebMvcConfigurationSupport {

    @Override
    protected void configureAsyncSupport(AsyncSupportConfigurer configurer) {
        configurer.setDefaultTimeout(5000);
        configurer.setTaskExecutor(mvcTaskExecutor());
    }

    @Bean
    public AsyncTaskExecutor mvcTaskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setThreadGroupName("mvc-executor");
        return taskExecutor;
    }
}
```
* WebMvcConfigurationSupport 클래스의 메서드를 오버라이드 해서 비동기 처리 모드로 구성 할 수 있습니다.
* 해당 메서드를 오버로드하면 setDefaultTimeout(), ThreadPoolTaskExecutor 설정을 할 수 있습니다.

### 비동기 컨트롤러 작성

```java
@Controller
@RequestMapping("reservationQuery")
@AllArgsConstructor
public class ReservationQueryController {

    private final ReservationService reservationService;

    @GetMapping
    public Callable<String> submitForm(@RequestParam("courtName") String courtName, Model model) {
        return () -> {
            // Create reservation list
            List<Reservation> reservations = java.util.Collections.emptyList();
            // Make a query if parameter is not null
            if (courtName != null) {
                Delayer.randomDelay(); // Simulate a slow service
                reservations = reservationService.query(courtName);
            }
            // Update model to include reservations
            model.addAttribute("reservations", reservations);
            // Return view as a string
            // Based on resolver configuration the reservationQuery view
            // will be mapped to a JSP in /WEB-INF/jsp/reservationQuery.jsp
            return "reservationQuery";
        };
    }
}
```
* `reservationService.query(courtName)` 메서드를 서버를 브로킹 하지 않은 상태로 호출할 수 있습니다.
* 특정 스레드가 요청을 도맡아 처리하다가 해제 되고 다른 스레드가 이어 받아 결괄르 반환합니다. 마지막 요청이 다시 DispatchrServlet에 전송되고 또 다른 스레드가 결과를 받아 처리합니다.