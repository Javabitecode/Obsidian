# Example

``` java
@ConfigurationProperties(prefix = "app")
@Getter
@Setter
public class AppProperties {
    private final Url url = new Url();
    private final KafkaTopic kafkaTopic = new KafkaTopic();
    @Getter
    @Setter
    public static class Url{
        private String resident;
        private String oc;
    }
    @Getter
    @Setter
    public static class KafkaTopic{
        private String residentEvent;
        private String carEvent;
    }
}
```