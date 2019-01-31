---
title: java-DateUtil 常用时间方法封装
date: 2019-01-09 22:02:09
tags: java
---

运用Calendar封装了个工具类，代码如下(主页请点击more阅读全文)
<!-- more -->	
```java
import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.LocalTime;
import java.time.ZoneId;
import java.time.ZonedDateTime;
import java.time.chrono.ChronoLocalDate;
import java.time.chrono.Chronology;
import java.time.chrono.MinguoChronology;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeFormatterBuilder;
import java.time.format.DecimalStyle;
import java.time.temporal.TemporalAccessor;
import java.util.Calendar;
import java.util.Date;
import java.util.Locale;
import java.util.TimeZone;

import org.apache.commons.lang.StringUtils;
import org.apache.log4j.Logger;

public class DateUtil {

	public final static String DATE_FORMAT = "yyyy-MM-dd";
	public final static String yyyyMMddHHmmss = "yyyyMMddHHmmss";
	public final static String DATETIME_FORMAT = "yyyy-MM-dd HH:mm:ss";
	public final static String yyyy_MM_dd_HH_mm = "yyyy-MM-dd HH:mm";
	public final static String yyyy_MM_dd_HH_mm_ss_SSS = "yyyy-MM-dd HH:mm:ss.SSS";
	public final static String yyyy_MM_dd_HH_mm_ss_S = "yyyy-MM-dd HH:mm:ss.S";
	public final static String yyyyMMddHHmmssSSS = "yyyyMMddHHmmssSSS";
	public final static String yyyyMMddHHmmssSSSSS = "yyyyMMddHHmmssSSSSSS";
	public final static String TIME_FORMAT = "HH:mm:ss";
	public final static String C_FORMAT="yyyy-MM-ddHHmm";
	public final static String yyyyMMdd="yyyyMMdd";
	public final static String yyyyMMddHH="yyyyMMddHH";
	public final static String DATE_DIRECTORY="yyyy/MM/dd";
	public final static SimpleDateFormat dateFormat = new SimpleDateFormat(
			DATE_FORMAT);
	public final static SimpleDateFormat dateTimeFormat = new SimpleDateFormat(
			DATETIME_FORMAT);
	public final static SimpleDateFormat timeFormat = new SimpleDateFormat(
			TIME_FORMAT);

	public static final String[] SEPARATORS = { " ", "," };
	private static Logger logger = Logger.getLogger(DateUtil.class.getName());

	/**
	 * 特定日期與今日日期比較 今天相等所給定日期 =0, 今天小於特定日期<0,今天大於特定日期>0
	 * 
	 * @param date
	 * @return
	 */
	public static int compareTo(Date date) {
		java.util.Calendar todayDate = java.util.Calendar.getInstance();
		java.util.Calendar c2 = java.util.Calendar.getInstance();

		Date today = Calendar.getInstance().getTime();
		todayDate.setTime(today);
		c2.setTime(date);
		int result = todayDate.compareTo(c2);
		
		return result;
	}

	/**
	 * 比對日期 yyyy-MM-dd
	 * @param date
	 * @return 0: 同一天 1: 已過期 -1: 未開始
	 */
	public static int compareDay(Date date) {
		int result = 0;
		
		DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
		//今天
		Date nd = java.sql.Date.valueOf(df.format(new Date()));
		//目的日期
		Date d1 = java.sql.Date.valueOf(DateUtil.convertDateToString(date));
		
		//同一天
		if ( nd.equals(d1) ){
			result = 0 ;
		}else if ( nd.before(d1) && !nd.after(d1)) {
		//未開始
			result = -1;
		}else if ( nd.after(d1) && !nd.before(d1) ) {
		//逾期
			result = 1;
		}
		
		return result;
	}

	/**
	 * java.util.Date 轉化 java.sql.Time
	 * 
	 * @param JavaDate
	 * @return
	 */
	public static java.sql.Time convertSqlTime(java.util.Date javaDate) {
		return new java.sql.Time(javaDate.getTime());
	}

	/**
	 * 取得現在時間
	 * 
	 * @return
	 */
	public static Date getCurrentDateTime() {
		Calendar now = Calendar.getInstance();
		// ==GMT標準時間往後加八小時
		timeFormat.setTimeZone(TimeZone.getTimeZone("GMT+8"));
		// ==取得目前時間
		String sdate = timeFormat.format(now.getTime());
		try {
			return timeFormat.parse(sdate);
		} catch (Exception e) {
			try {
				return timeFormat.parse(sdate);
			} catch (Exception ex) {
			}
		}
		return null;
	}

	/**
	 * 取得時區
	 */
	public static Date getlocalTime() {
		Date localTime = null;
		// local time to GMT time
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
		sdf.setTimeZone(TimeZone.getTimeZone("GMT"));
		sdf.setLenient(false);

		// GMT time to local time
		try {
			localTime = sdf.parse(sdf.format(System.currentTimeMillis()));
			sdf.setTimeZone(TimeZone.getDefault());
		} catch (ParseException e) {
			e.printStackTrace();
		}
		return localTime;
	}

	/**
	 * java Date轉換為字串
	 * 
	 * @param currDate
	 *            java.util.Date
	 * @param format
	 *            字串格式
	 * @return
	 */
	public static String converToString(java.util.Date date, String format) {
		if (date == null) {
			return "";
		}
		SimpleDateFormat dtFormatdB = null;
		try {
			dtFormatdB = new SimpleDateFormat(format);
			return dtFormatdB.format(date);
		} catch (Exception e) {
			dtFormatdB = new SimpleDateFormat(DATETIME_FORMAT);
			try {
				return dtFormatdB.format(date);
			} catch (Exception ex) {
			}
		}
		return null;
	}

	/**
	 * 轉換[時間字串]為java Date
	 * 
	 * @param date
	 *            時間字串 例如 "2014-12-31 18:30:00";
	 * @param format
	 *            傳換時間格式
	 * @return java.util.Date
	 */
	public static java.util.Date convertToDate(String date, String format) {
		if (date == null) {
			return null;
		}
		SimpleDateFormat dtFormatdB = null;
		try {
			dtFormatdB = new SimpleDateFormat(format);
			dtFormatdB.setLenient(false);
			return dtFormatdB.parse(date);
		} catch (Exception e) {
			dtFormatdB = new SimpleDateFormat(DATETIME_FORMAT);
			try {
				dtFormatdB.setLenient(false);
				return dtFormatdB.parse(date);
			} catch (Exception ex) {
			}
		}
		return null;
	}

	/**
	 * Timestamp化為字符串
	 * 
	 * @param date
	 * @param format
	 *            定義格式
	 * @return
	 */
	public static String convertToString(java.sql.Timestamp timestamp,
			String format) {
		SimpleDateFormat df = null;
		String str = "";
		if (null == format) {
			df = new SimpleDateFormat(DATETIME_FORMAT);			
		} else {
			df = new SimpleDateFormat(format);
		}
		str = df.format(timestamp);
		return str;
	}

	/**
	 * java.util.Date轉化為Timestamp
	 * 
	 * @param date
	 * @param format
	 *            if null default "yyyy-MM-dd HH:mm:ss"
	 * @return
	 */
	public static java.sql.Timestamp convertToTimestamp(java.util.Date date,
			String format) {
		if (date == null) {
			return null;
		}
		SimpleDateFormat df = null;
		if (null == format)
			df = new SimpleDateFormat(DATETIME_FORMAT);
		else
			df = new SimpleDateFormat(format);
		df.setLenient(false);
		String time = df.format(date);
		java.sql.Timestamp ts = java.sql.Timestamp.valueOf(time);
		return ts;
	}
	/**
	 * 字符串轉化為Timestamp
	 * 
	 * @param date
	 * @param format
	 *            if null default "yyyy-MM-dd HH:mm:ss"
	 * @return
	 */
	public static java.sql.Timestamp convertToTimestamp(String dateString,
			String format) {
		if (dateString == null) {
			return null;
		}
		SimpleDateFormat df = null;
		if (null == format)
			df = new SimpleDateFormat(DATETIME_FORMAT);
		else
			df = new SimpleDateFormat(format);
		java.util.Date date = null;
		try {
			df.setLenient(false);
			date = df.parse(dateString);
		} catch (ParseException e) {
			e.printStackTrace();
		}
		java.sql.Timestamp ts = new java.sql.Timestamp(date.getTime());
		return ts;
	}

	public static String convertTWDate(String AD, String beforeFormat,
			String afterFormat) {// 轉年月格式
		if (AD == null)
			return "";
		SimpleDateFormat df4 = new SimpleDateFormat(beforeFormat);
		SimpleDateFormat df2 = new SimpleDateFormat(afterFormat);
		Calendar cal = Calendar.getInstance();
		try {
			cal.setTime(df4.parse(AD));
			if (cal.get(Calendar.YEAR) > 1492)
				cal.add(Calendar.YEAR, -1911);
			else
				cal.add(Calendar.YEAR, +1911);
			return df2.format(cal.getTime());
		} catch (Exception e) {
			e.printStackTrace();
			return null;
		}
	}

	/**
	 * 規範民國年format格式
	 * 
	 * @author HOWARD
	 *
	 */
	private static enum MinguoStyle {
		MINGUO_DATE_CHT("民國yyy年MM月dd日"), MINGUO_DATE_TIME_CHT("民國yyy年MM月dd日",
				"HH時mm分ss秒"), MINGUO_DATE_EN("yyy-MM-dd"), MINGUO_DATE_TIME_EN(
				"yyy-MM-dd", "HH:mm:ss"), MINGUO_DATE_NON("yyyMMdd");

		private String dateStyle;
		private String timeStyle;

		MinguoStyle(String dateStyle, String timeStyle) {
			logger.debug("MinguoStyle K:dateStyle,timeStyle -> " + this.name()
					+ " : " + dateStyle + "," + timeStyle);
			this.dateStyle = dateStyle;
			this.timeStyle = timeStyle;
		}

		MinguoStyle(String dateStyle) {
			this(dateStyle, "");
		}

	}

	/**
	 * Date type convert to LocalDateTime
	 * 
	 * @param date
	 * @return LocalDateTime
	 */
	public static LocalDateTime toLocalDateTime(Date date) {
		logger.debug("toLocalDateTime date : " + date);
		logger.debug("toLocalDateTime ZoneID : " + ZoneId.systemDefault());
		return LocalDateTime
				.ofInstant(date.toInstant(), ZoneId.systemDefault());
	}

	/**
	 * Date轉民國年字串主程式
	 * 
	 * @param srcDate
	 * @param minguoStyle
	 * @return 依據MinguoStyle指定輸出樣式 ex: makeMinguoString(new Date(),
	 *         MinguoStyle.MINGUO_DATE_TIME_CHT);
	 */
	public static String makeMinguoString(Date srcDate, MinguoStyle minguoStyle) {
		logger.debug("makeMinguoString srcDate : " + srcDate);
		logger.debug("makeMinguoString minguoStyle : " + minguoStyle);
		// Date to LocalDateTime
		LocalDateTime localeDateTime = toLocalDateTime(srcDate);
		LocalDate localDate = localeDateTime.toLocalDate();
		LocalTime localTime = localeDateTime.toLocalTime();
		//
		String minguoDate = localDateToMinguoString(localDate, minguoStyle);
		;
		String minguoTime = localeTimeToString(localTime, minguoStyle);
		;

		logger.info("makeMinguoString : " + minguoDate + minguoTime);
		return minguoDate + minguoTime;
	}

	/**
	 * 將西元年LocaleDate轉為民國年格式字串
	 * 
	 * @param localDate
	 * @param minguoStyle
	 * @return 民國年字串
	 */
	public static String localDateToMinguoString(LocalDate localDate,
			MinguoStyle minguoStyle) {
		logger.debug("localDateToMinguoString localDate : " + localDate);
		logger.debug("localDateToMinguoString minguoStyle : " + minguoStyle);
		Locale locale = Locale.getDefault(Locale.Category.FORMAT);
		Chronology chrono = MinguoChronology.INSTANCE;
		ChronoLocalDate cDate;
		cDate = chrono.date(localDate);
		DateTimeFormatter dateFormatter = DateTimeFormatter
				.ofPattern(minguoStyle.dateStyle).withLocale(locale)
				.withChronology(chrono)
				.withDecimalStyle(DecimalStyle.of(locale));
		return dateFormatter.format(cDate);
	}

	/**
	 * 將LocalTime轉為format字串
	 * 
	 * @param time
	 * @param minguoStyle
	 * @return string literal time , like : '12:00:00' or '12時00分00秒'
	 */
	public static String localeTimeToString(LocalTime time,
			MinguoStyle minguoStyle) {
		logger.debug("localeTimeToString time : " + time);
		logger.debug("localeTimeToString minguoStyle : " + minguoStyle);
		String timeString = "";
		if (!"".equals(minguoStyle.timeStyle)) {
			timeString = " "
					+ time.format(DateTimeFormatter
							.ofPattern(minguoStyle.timeStyle));
		}
		return timeString;
	}

	/**
	 * 轉換字串型式的民國年為LocalDate(西元年)
	 * 
	 * @param text
	 * @param minguoStyle
	 * @return 該民國年的LocalDate(西元年) ex:minguoStringToLocalDate("民國104年02月05日",
	 *         MinguoStyle.MINGUO_DATE_CHT)
	 */
	private static LocalDate minguoDateStringToLocalDate(String text,
			MinguoStyle minguoStyle) {
		logger.debug("minguoDateStringToLocalDate text : " + text);
		logger.debug("minguoDateStringToLocalDate minguoStyle : " + minguoStyle);
		Locale locale = Locale.getDefault(Locale.Category.FORMAT);
		Chronology chrono = MinguoChronology.INSTANCE;
		DateTimeFormatter df = new DateTimeFormatterBuilder().parseLenient()
				.appendPattern(minguoStyle.dateStyle).toFormatter()
				.withChronology(chrono)
				.withDecimalStyle(DecimalStyle.of(locale));
		TemporalAccessor temporal = df.parse(text);
		ChronoLocalDate cDate = chrono.date(temporal);
		return LocalDate.from(cDate);
	}

	/**
	 * 以時間字串取得LocalTime物件
	 * 
	 * @param text
	 * @param minguoStyle
	 * @return LocalTime : If text is blank or null will return 'now' ,else as
	 *         your input . ex:stringToLocalTime("14時00分03秒",
	 *         MinguoStyle.TIME_CHT)
	 */
	private static LocalTime stringToLocalTime(String text,
			MinguoStyle minguoStyle) {
		logger.debug("stringToLocalTime text : " + text);
		logger.debug("stringToLocalTime timeStyle : " + minguoStyle.timeStyle);
		LocalTime localTime;
		if ("".equals(text) || null == text) {
			localTime = LocalTime.now();
		} else {
			localTime = LocalTime.parse(text,
					DateTimeFormatter.ofPattern(minguoStyle.timeStyle));
		}
		return localTime;
	}

	/**
	 * 轉換為java.util.Date實體
	 * 
	 * @param localDate
	 * @param localTime
	 * @return Date
	 */
	public static Date localDateAndTimeToLegacyDate(LocalDate localDate,
			LocalTime localTime) {
		logger.debug("localDateAndTimeToLegacyDate localDate : " + localDate);
		logger.debug("localDateAndTimeToLegacyDate localTime : " + localTime);
		LocalDateTime ldt = localDate.atTime(localTime);
		ZonedDateTime zdt = ldt.atZone(ZoneId.systemDefault());
		return Date.from(zdt.toInstant());
	}

	/**
	 * 將日期和時間分開，此方法預設傳入字串格式日期與時間是用' ' 或 ','分隔 另外會指定傳入的MinguoStyle
	 * 
	 * @param minguoString
	 * @return [0]-日期,[1]-時間 if exist ex :
	 *         resolveMinguoString("104/01/01 12:00:00");
	 */
	public static String[] resolveMinguoString(String minguoString) {
		logger.debug("resolveMinguoString minguoString : " + minguoString);
		minguoString = minguoString.trim();
		String[] resolved = new String[2];
		String splitor = "";
		for (String separator : SEPARATORS) {
			logger.debug("resolveMinguoString separator : '" + separator + "'");
			if (minguoString.indexOf(separator) > 0) {
				splitor = separator;
				break;
			}
		}
		if (splitor.equals("")) {
			resolved[0] = minguoString;
			resolved[1] = "";
		} else {
			resolved = minguoString.split(splitor);
		}
		logger.debug("resolveMinguoString splitor : '" + splitor + "'");
		logger.debug("resolveMinguoString resolved[0] : " + resolved[0]);
		logger.debug("resolveMinguoString resolved[1] : " + resolved[1]);
		return resolved;
	}

	/**
	 * 由頁面取得字串型態的日期轉Date的主程式。
	 * 
	 * @param minguoString
	 *            目前只允許'104/01/01'or'104/01/01 12:00:00'格式轉換
	 * @return java.util.Date
	 */
	public static Date makeDate(String minguoString) {
		logger.debug("makeDate minguoString : " + minguoString);
		Date date;
		LocalDate localDate;
		LocalTime localTime;
		MinguoStyle minguoStyle = null;
		String[] mingouContent = resolveMinguoString(minguoString);
		if (mingouContent[1].equals("")) {
			minguoStyle = MinguoStyle.MINGUO_DATE_EN;
		} else {
			minguoStyle = MinguoStyle.MINGUO_DATE_TIME_EN;
		}
		localDate = minguoDateStringToLocalDate(mingouContent[0], minguoStyle);
		localTime = stringToLocalTime(mingouContent[1], minguoStyle);
		date = localDateAndTimeToLegacyDate(localDate, localTime);
		logger.debug("makeDate date : " + date);
		return date;
	}

	/**
	 * 民國年字串轉成資料庫日期 yyy-MM-dd HH:mm:ss -> Date
	 * 
	 * @param rocDate
	 * @return
	 */
	public static Date convertROCToDate(String rocDate) {
		if (StringUtils.isBlank(rocDate))
			return null;
		if (rocDate.length() == 9 || rocDate.length() == 8) {
			rocDate = rocDate + " 00:00:00";
		}
		rocDate = StringUtils.leftPad(rocDate, 18, "0");
		String year = String
				.valueOf(Integer.parseInt(rocDate.substring(0, 3)) + 1911);
		String newDate = year + rocDate.substring(3);
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		try {
			return sdf.parse(newDate);
		} catch (Exception e) {
			return null;
		}
	}

	/**
	 * 資料庫日期顯示在畫面上(輸入欄位) yyyy-MM-dd HH:mm:ss -> yyy-MM-dd
	 * 
	 * @param rocDate
	 * @return
	 */
	public static String convertDateToROC(Date date) {
		if (date == null)
			return "";
		Calendar cal = Calendar.getInstance();
		cal.setTime(date);
		int year = cal.get(Calendar.YEAR) - 1911;
		int month = cal.get(Calendar.MONTH) + 1;
		int day = cal.get(Calendar.DAY_OF_MONTH);
		String rocDate = StringUtils.leftPad(String.valueOf(year), 3, "0") + "-"
				+ StringUtils.leftPad(String.valueOf(month), 2, "0") + "-"
				+ StringUtils.leftPad(String.valueOf(day), 2, "0");
		return rocDate;
	}

	/**
	 * 資料庫日期顯示在畫面上(輸入欄位) yyy-MM-dd HH:mm:ss -> yyy-MM-dd HH:mm:ss
	 * 
	 * @param rocDate
	 * @return
	 */
	public static String convertDateTimeToROC(Date date) {
		if (date == null)
			return "";
		Calendar cal = Calendar.getInstance();
		cal.setTime(date);
		int year = cal.get(Calendar.YEAR) - 1911;
		int month = cal.get(Calendar.MONTH) + 1;
		int day = cal.get(Calendar.DAY_OF_MONTH);
		int hour = cal.get(Calendar.HOUR_OF_DAY);
		int minute = cal.get(Calendar.MINUTE);
		int second = cal.get(Calendar.SECOND);
		String rocDate = StringUtils.leftPad(String.valueOf(year), 3, "0") + "-"
				+ StringUtils.leftPad(String.valueOf(month), 2, "0") + "-"
				+ StringUtils.leftPad(String.valueOf(day), 2, "0") + " "
				+ StringUtils.leftPad(String.valueOf(hour), 2, "0") + ":"
				+ StringUtils.leftPad(String.valueOf(minute), 2, "0") + ":"
				+ StringUtils.leftPad(String.valueOf(second), 2, "0");
		return rocDate;
	}

	/**
	 * 資料庫日期顯示在畫面上(輸入欄位) yyy-MM-dd HH:mm:ss -> HH:mm
	 * 
	 * @param rocDate
	 * @return
	 */
	public static String convertDateToTime(Date date) {
		if (date == null)
			return "";
		Calendar cal = Calendar.getInstance();
		cal.setTime(date);
		int hour = cal.get(Calendar.HOUR_OF_DAY);
		int min = cal.get(Calendar.MINUTE);
		return StringUtils.leftPad(String.valueOf(hour), 2, "0") + ":"
				+ StringUtils.leftPad(String.valueOf(min), 2, "0");
	}
	
	/**
	 * 資料庫日期顯示在畫面上(輸入欄位) Date -> yyyy-MM-dd
	 * 
	 * @param date
	 * @return
	 */
	public static String convertDateToString(Date date) {
		if (date == null)
			return "";
		Calendar cal = Calendar.getInstance();
		cal.setTime(date);
		int year = cal.get(Calendar.YEAR);
		int month = cal.get(Calendar.MONTH) + 1;
		int day = cal.get(Calendar.DAY_OF_MONTH);
		String dateString = String.valueOf(year) + "-"
				+ StringUtils.leftPad(String.valueOf(month), 2, "0") + "-"
				+ StringUtils.leftPad(String.valueOf(day), 2, "0");
		return dateString;
	}
	
	/**
	 * 西元年字串轉成資料庫日期 yyyy-MM-dd HH:mm:ss -> Date
	 * 
	 * @param dateString
	 * @return
	 */
	public static Date convertStringToDate(String dateString) {
		if (StringUtils.isBlank(dateString))
			return null;
		if (dateString.length() == 10) {
			dateString = dateString + " 00:00:00";
		}
		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
		try {
			return sdf.parse(dateString);
		} catch (Exception e) {
			return null;
		}
	}
	
	/**
	 * java Date轉換為西元字串 年需大於1911 -> true:民國yyy年MM月dd日,false:yyy/MM/dd
	 * @param currDate
	 *            java.util.Date
	 * @param format
	 *            字串格式
	 * @return
	 */
	public static String converToROCString(java.util.Date date,boolean type) {
		
		if (date == null) {
			return "";
		}else{

			int yyyy=Integer.parseInt(new SimpleDateFormat("yyyy").format(date));
			
			if(date.getTime()<Long.parseLong("-1861948800000")||yyyy==9999){
				return "";
			}else{
				String format=type==true?((yyyy-1911)+"年MM月dd日"):(String.format("%03d",(yyyy-1911))+"/MM/dd");
				return new SimpleDateFormat(format).format(date);
			}
		}
	}
	
	/**
	  * 取得傳入時間前一天00:00:00的時間
	  * @param date
	  * @return
	  */
	 public static Date findYesterday(Date date){
		  if(date==null){
			  return null;
		  }
		  Calendar cal = Calendar.getInstance();
		  cal.setTime(date);
		  cal.add(Calendar.DATE, -1);
		  return convertStringToDate(convertDateToString(cal.getTime()));
	 }
	 
	 /**
	  * 與今日間隔天數，>0 n天 目標日期低於今天n天 ==> 已過期 ， <0 -n天 目標日期超過今日n天  ==> 未到期
	  * @param input String yyyy-MM-dd
	  * @return int 
	  */
	 public static int intervalDay(String input) {
		 
		 Date d1 = convertToDate(input, DateUtil.DATE_FORMAT);
		 
		 String now = converToString(new Date(), DateUtil.DATE_FORMAT);
		 Date d2 = convertToDate(now, DateUtil.DATE_FORMAT);;
		
		 long diff = d1.getTime() - d2.getTime();
		 Integer intVal = ((Number)(diff/(1000*60*60*24))).intValue();
		 
		 logger.info("Difference is " + intVal.intValue() + " days.");
		 return intVal.intValue();
	 }
	 
	/**
	 * 轉換 - 為年,月,日 EX: 2017-03-24 > 2017年03月24日, 106-03-24 > 106年03月24日
	 * @param dateString yyyy-MM-dd or yyy-MM-dd
	 * @return String. null if exception, else return result String
	 */
	public static String convertDashToChineseYear(String dateString) {
		String result = null; 
		try {
			result = dateString.split("-")[0] + "年"  + dateString.split("-")[1] + "月" + dateString.split("-")[2] + "日";
		} catch (Exception e) {
			e.printStackTrace();
			logger.error("輸入格式錯誤");
			return null;
		}
		
		return result;
	}
	
	/**
	 * 取得當月最後一天
	 * @param date
	 * @return
	 */
	public static Date getLastDayOfMonth(Date date) {
		try {
			Calendar calendar = Calendar.getInstance();
			calendar.setTime(date);
			calendar.add(Calendar.MONTH, 1); 
			calendar.set(Calendar.DAY_OF_MONTH, 1);  
			calendar.add(Calendar.DATE, -1);
			
			return calendar.getTime();
		} catch (Exception e) {
			e.printStackTrace();
			logger.error(e);
			return null;
		}
	}
	
	/**
	 * 取得上個月第一天
	 * @param date
	 * @return
	 */
	public static Date getFirstDayOfLastMonth(Date date) {
		try {
			if(date==null){
				date = new Date();
			}
			Calendar calendar = Calendar.getInstance();
			calendar.setTime(date);
			calendar.add(Calendar.MONTH, -1);
			calendar.set(Calendar.DAY_OF_MONTH, 1);  
			return calendar.getTime();
		} catch (Exception e) {
			e.printStackTrace();
			logger.error(e);
			return null;
		}
	}
	
	/**
	 * 取得傳入時間加N天的時間 (減傳負數)
	 * @param date
	 * @param count
	 * @return
	 */
	public static Date addNDay(Date date, int count){
		if(date==null){
			date = new Date();
		}
		Calendar calendar = Calendar.getInstance();
		calendar.setTime(date);
		calendar.add(Calendar.DATE, count);  
		return calendar.getTime();
	}
	
	/**
	 * 取得昨日區間
	 * @param year
	 * @param month
	 * @param day
	 * @return
	 */
	private static String getYesterDay(int year, int month, int day){
		Calendar cal = Calendar.getInstance();
		cal.set(year,month-1,day);
		cal.add(Calendar.DATE , -1);
		String yesterday = DateUtil.convertDateToString(cal.getTime());
		return yesterday+"~"+yesterday;
	}
	
	/**
	 * 依照年月日，取得前一个月的时间区间
	 * 如2019/01/10,则得到2018-12-10~2019-01-09
	 * @param year 年
	 * @param month 月
	 * @param day 日
	 * @return
	 */
	public static String interval(int year,int month,int day){

		//日 -1
		if(day == 0 ||  day > 31){
			day = 31;
		}else if(day == 1){
			day = 31;
			if(month == 1){
				month = 12;
				// 2018/1/4 Will DJTSQCA-1242 add
				year--;
				// end
			}else {
				--month;
			}
		}else{
			--day;
		}

		String dateStr =  null;
		String lestDateStr =  null;

		//取得當月
		Calendar cal = Calendar.getInstance();
		cal.set(year,month-1,1);

		int updateDateCount = 0;

		//當下月份天數是否大於日
		//計算需要溢位的天數
		if(day > cal.getActualMaximum(Calendar.DAY_OF_MONTH)){
			updateDateCount = day - cal.getActualMaximum(Calendar.DAY_OF_MONTH);
			cal.set(Calendar.DATE,day - updateDateCount);
		}else{
			cal.set(Calendar.DATE,day);
		}

		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
		dateStr = sdf.format(cal.getTime());

		//上一個月的總天數
		Calendar lastCal = (Calendar) cal.clone();
		lastCal.add(Calendar.MONTH,-1);
		int dayCount = lastCal.getActualMaximum(Calendar.DAY_OF_MONTH) - 1 - updateDateCount;

		//上一個月份是否有更改溢位Temp
		Calendar calTemp = Calendar.getInstance();
		calTemp.set(year,month-2,1);
		//當上月份是否大於日
		if(Integer.valueOf(day) > calTemp.getActualMaximum(Calendar.DAY_OF_MONTH)){
			int reverDateCount = day - calTemp.getActualMaximum(Calendar.DAY_OF_MONTH);
			cal.add(Calendar.DATE,-reverDateCount);
		}
		//相減上個月總天數
		cal.add(Calendar.DATE, - dayCount);
		lestDateStr = sdf.format(cal.getTime());

		return lestDateStr+ "~" + dateStr;
	}
}

```
