# 108-bigdatalogging

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

