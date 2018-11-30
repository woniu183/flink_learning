# 1、compile command

- mvn assembly:assembly
- product.TrackRealTimeAcquisitionHackAttack 定义了wzh的流量数据，基于eventtime进行分析。

# 2、（todo）使用如下的warkmark函数不生效
```scala
package product

import org.apache.flink.streaming.api.functions.AssignerWithPeriodicWatermarks
import org.apache.flink.streaming.api.watermark.Watermark

class BoundedGenerator extends AssignerWithPeriodicWatermarks[(Long, String, String, String, String, String, String, String, String, Long, String)] {

  val maxOutOfOrderness = 3500L // 3.5 seconds

  var currentMaxTimestamp: Long = _

  override def extractTimestamp(element: (Long, String, String, String, String, String, String, String, String, Long, String), previousElementTimestamp: Long): Long = {
    val timestamp = element._10
    timestamp;
  }

  override def getCurrentWatermark(): Watermark = {
    // return the watermark as current highest timestamp minus the out-of-orderness bound
    new Watermark(currentMaxTimestamp - maxOutOfOrderness)
  }
}
```
需要这样定义：


```scala
  private val extractor = new AscendingTimestampExtractor[Tuple11[Long,String,String,String,String,String,String,String,String,Long,String]]() {
    override def extractAscendingTimestamp(element: Tuple11[Long,String,String,String,String,String,String,String,String,Long,String]): Long = element._10
  }
```


