# java.util.logging

## Log to file with formatting
```java
Logger logger = Logger.getLogger("logger name");
logger.setUseParentHandlers(false);
try {
    // for outputting to the file
    FileHandler fileHandler = new FileHandler("file name");
    fileHandler.setFormatter(new SimpleFormatter() {
        private DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("MM/dd/yyyy HH:mm:ss");
        private String logFormat = "[%s]: %s\n";

        // formats our log
        @Override
        public String format(LogRecord record) {
            LocalDateTime now = LocalDateTime.now();
            String log = String.format(logFormat, dateTimeFormatter.format(now), record.getMessage());
            // for outputting to the console if you want to do that as well
            System.out.print(log);
            return log;
        }
    });
    logger.addHandler(fileHandler);
} catch (SecurityException e) {
    e.printStackTrace();
    System.exit(-1);
} catch (IOException e) {
    e.printStackTrace();
    System.exit(-1);
}
```