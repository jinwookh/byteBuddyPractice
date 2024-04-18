# 목적
"hi"를 출력하도록 한 코드를 byte-buddy를 이용하여 "rabbit"을 출력하도록 만든다.

# 과정
## java agent로 사용할 jar 파일 만들기
```
javac -cp "byte-buddy-1.14.13.jar" MasulsaAgent.java
```
```
jar cfm MasulsaAgent.jar manifest.txt MasulsaAgent.class bytebuddy.jar
```

## java agent를 활용해서 Masulsa(main 메서드) 실행
```
java -javaagent:MasulsaAgent.jar -cp .:byte-buddy-1.14.13.jar Masulsa
```

# 결과
본래 코드:
```
public String pullOut() {
    return "hi";
}
```
byte-buddy로 rabbit! 출력하도록 수정
```
public class MasulsaAgent {
    public static void premain(String agentArgs, Instrumentation inst) {
        new AgentBuilder.Default()
                .type(ElementMatchers.any())
                .transform(((builder, typeDescription, classLoader, module, protectionDomain) ->
                        builder.method(named("pullOut")).intercept(FixedValue.value("Rabbit!")))).installOn(inst);
    }
}
```
