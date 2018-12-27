# BufferedWriter多线程上传示例 {#concept_dxx_v4g_vdb .concept}

本文通过代码示例向您介绍如何使用BufferedWriter接口实现多线程上传。

```java
class UploadThread extends Thread {
  private UploadSession session;
  private static int RECORD_COUNT = 1200;
  public UploadThread(UploadSession session) {
    this.session = session;
  }
  @Override
  public void run() {
    RecordWriter writer = session.openBufferedWriter();
    Record r = session.newRecord();
    for (int i = 0; i < RECORD_COUNT; i++) {
      r.setBigint(0, i);
      writer.write(r);
    }
    writer.close();
  }
}

public class Example {
  public static void main(String args[]) {
    // 初始化MaxCompute和tunnel的代码
    TableTunnel.UploadSession uploadSession = tunnel.createUploadSession(projectName, tableName);
    UploadThread t1 = new UploadThread(uploadSession);
    UploadThread t2 = new UploadThread(uploadSession);
    t1.start();
    t2.start();
    t1.join();
    t2.join();
    uploadSession.commit();
  }
}
```

