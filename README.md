# 108-bigdatalogging
每次在寫程式的時候，我們一定會把程式運行中的訊息記錄下來。在 Python 中，我們很順手地，就會做下面這件事:
    print( "The program meets error %d" % error_code )
在電腦領域，紀錄檔案（logfile）是一個記錄了發生在執行中的作業系統或其他軟體中的事件的檔案，或者記錄了在網路聊天軟體的用戶之間傳送的訊息。 紀錄檔記錄（Logging）是指儲存紀錄檔的行為。 每個系統都必不可少會需要一個log類，方便了解系統的執行狀況和排錯，python本身。

已經提供了一個logger了，很強大，只要稍微封裝一下就可以放到自己的系統了
``` python
import logging
import os
import sys
class logger(object):
  """Class provides methods to perform logging."""
  m_logger = None
  def __init__(self, opts, logfile):
    """Set the default logging path."""
    self.opts = opts
    self.myname = 'dxscs'
    self.logdir = '.'
    self.logfile = logfile
    self.filename = os.path.join(self.logdir, self.logfile)
  def loginit(self):
    """Calls function LoggerInit to start initialising the logging system."""
    logdir = os.path.normpath(os.path.expanduser(self.logdir))
    self.logfilename = os.path.normpath(os.path.expanduser(self.filename))
    if not os.path.isdir(logdir):
      try:
        os.mkdir(logdir)
      except OSError, e:
        msg = ('(%s)'%e)
        print msg
        sys.exit(1)
    self.logger_init(self.myname)
  def logger_init(self, loggername):
    """Initialise the logging system.
    This includes logging to console and a file. By default, console prints
    messages of level WARN and above and file prints level INFO and above.
    In DEBUG mode (-D command line option) prints messages of level DEBUG
    and above to both console and file.
    Args:
     loggername: String - Name of the application printed along with the log
     message.
    """
    fileformat = '[%(asctime)s] %(name)s: [%(filename)s: %(lineno)d]: %(levelname)-8s: %(message)s'
    logger.m_logger = logging.getLogger(loggername)
    logger.m_logger.setLevel(logging.INFO)
    self.console = logging.StreamHandler()
    self.console.setLevel(logging.CRITICAL)
    consformat = logging.Formatter(fileformat)
    self.console.setFormatter(consformat)
    self.filelog = logging.FileHandler(filename=self.logfilename, mode='w ')
    self.filelog.setLevel(logging.INFO)
    self.filelog.setFormatter(consformat)
    logger.m_logger.addHandler(self.filelog)
    logger.m_logger.addHandler(self.console)
    if self.opts['debug'] == True:
      self.console.setLevel(logging.DEBUG)
      self.filelog.setLevel(logging.DEBUG)
      logger.m_logger.setLevel(logging.DEBUG)
    if not self.opts['nofork']:
      self.console.setLevel(logging.WARN)
  def logstop(self):
    """Shutdown logging process."""
    logging.shutdown()
#test    
if __name__ == '__main__':
  #debug mode & not in daemon
  opts = {'debug':True,'nofork':True}
  log = logger(opts, 'dxscs_source.log')
  log.loginit()
  log.m_logger.info('hello,world')
ˋˋˋ 
終端和檔案中都顯示有：[2012-09-06 16:56:01,498] dxscs: [logger.py: 88]: INFO    : hello,world

如果只需要顯示在檔案中可以將debug和nofork選項都置為false

這個寫法的原則很重要，請都用下面所說的想法來設計 logging 相關的程式:

每個 module, class, 都用自已的logger, 當你寫的code, 是一個被人使用的module, 請不要去做多餘的設定。
logger 的名字為了要能方便找出問題，所以不要亂取，通常都是以 module 或是 class 的名稱來命名。
logging 的設定，由整個應用程式的main 函式來決定, 比如說決定該把log 寫到檔案或是將log 傳送到syslogd .
我們目前是為了簡單好用才以 logging.basicConfig 當作例子。更實際的例子會用 logging.config.fileConfig. 

希望本文所述對大家的Python程式設計有所幫助。
