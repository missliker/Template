# Class and Enum

有关于class和enum，这里写了一个简单的日志类来作为参考

```c++
class Log {
  public:
    enum Level { LevelError = 0, LevelWarning, LevelInfo };

  private:
    Level m_LogLevel = LevelInfo;

  public:
    void SetLevel(Level level) { m_LogLevel = level; }

    void PrintLevel() { std::cout << "[LEVEL]: " << m_LogLevel << std::endl; }

    void Error(const char* message) {
        if (m_LogLevel >= LevelError) {
            std::cout << "[ERROR]: " << message << std::endl;
        }
    }

    void Warning(const char* message) {
        if (m_LogLevel >= LevelWarning) {
            std::cout << "[WARNING]: " << message << std::endl;
        }
    }

    void Info(const char* message) {
        if (m_LogLevel >= LevelInfo) {
            std::cout << "[INFO]: " << message << std::endl;
        }
    }
};
```

用法：
```c++
int main() {
    cin.tie(nullptr)->sync_with_stdio(false);
    cout << fixed << setprecision(20);

    Log log;
    log.PrintLevel();
    log.Info("Info");

    log.SetLevel(Log::LevelError);
    log.PrintLevel();
    log.Warning("Warning");
    
    /*
    	输出结果：
        [LEVEL]: 2
        [INFO]: Info
        [LEVEL]: 0
	*/

    return 0;
}
```