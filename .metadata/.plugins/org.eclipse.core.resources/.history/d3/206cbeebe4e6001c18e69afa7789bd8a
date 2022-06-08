package global.sesoc.web5.controller;

import javax.servlet.http.HttpSession;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.ModelAttribute;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.SessionAttributes;
import org.springframework.web.bind.support.SessionStatus;

import global.sesoc.web5.dao.MemberDAO;
import global.sesoc.web5.vo.Member;

/**
 * 회원 정보 관련 콘트롤러
 */
@Controller
@RequestMapping("member")
public class MemberController {
	private static final Logger logger = LoggerFactory.getLogger(MemberController.class);
	
	@Autowired
	MemberDAO dao;
	
	/**
	 * 회원 가입 폼 보기
	 */
	@RequestMapping (value="join", method=RequestMethod.GET)
	public String joinForm(Model model) {
		return "memberjsp/joinForm";
	}

	/**
	 * 회원 가입 처리
	 */
	@RequestMapping (value="join", method=RequestMethod.POST)
	public String join(Model model, Member member) {
		
		int result = dao.insert(member);
		if (result != 1) {
			return "memberjsp/joinForm";
		}
		return "redirect:/";
	}
	
	/**
	 * ID 중복확인 화면 보기
	 */
	@RequestMapping (value="idcheck", method=RequestMethod.GET)
	public String idcheck(Model model) {
		return "memberjsp/idcheck";
	}

	/**
	 * ID 중복 검사
	 */
	@RequestMapping (value="idcheck", method=RequestMethod.POST)
	public String idcheck(Model model, String searchId) {
		Member member = dao.getMember(searchId);
		model.addAttribute("member", member);		//검색 결과가 없으면 null
		model.addAttribute("searchId", searchId); 	//사용자가 검색한 ID
		return "memberjsp/idcheck";
	}

	/**
	 * 로그인 폼으로 이동
	 */
	@RequestMapping (value="login", method=RequestMethod.GET)
	public String login() {
		return "memberjsp/loginForm";
	}

	/**
	 * 로그인 처리
	 */
	@RequestMapping (value="login", method=RequestMethod.POST)
	public String login(String id, String password, Model model, HttpSession session) {
		Member member = dao.getMember(id);
		
		//ID가 존재하고 비밀번호도 맞으면 세션에 ID와 이름을 저장하고 메인화면으로 리다이렉트
		if (member != null && member.getPassword().equals(password)) {
			session.setAttribute("loginId", member.getId());
			session.setAttribute("loginName", member.getName());
			return "redirect:/";
		}
		//ID가 없거나 비밀번호가 틀리면 로그인 화면으로 다시 포워딩
		else {
			model.addAttribute("errorMsg", "ID 또는 비밀번호가 틀립니다.");
			return "memberjsp/loginForm";
		}
	}
	
	/**
	 * 로그아웃
	 */
	@RequestMapping (value="logout", method=RequestMethod.GET)
	public String logout(HttpSession session) {
		session.removeAttribute("loginId");
		session.removeAttribute("loginName");
		return "redirect:/";
	}
	
	/**
	 * 개인정보 수정 폼으로 이동
	 여기 같은 경우는 로그인 안한사람은 필요가 없는 곳이지
	 만약에 로그인 안하고 들어오면 널값이 들어와서 오류야 
	 그런데 만약 주소를 알고 직링으로 오면? 뚫리는거지 
	 
	 몇개의 경로를 어디에 적어야해 */
	@RequestMapping (value="update", method=RequestMethod.GET)
	public String update(Model model, HttpSession session) {
		String loginId = (String) session.getAttribute("loginId");
		/*if(liginId == null) 이런식으로 처리하려고 했을 것이지 아마
		 * 근데 그러면 이거를 몇번을 각각 써야 하는 거야.. 고칠 때 어케 할거야 
		 * 그래서 우리는 정리를 하고 한번에 써 인터셉터라고 이거는 스프링이 주는 기능이야*/
		Member member = dao.getMember(loginId);
		model.addAttribute("member", member);
		return "memberjsp/updateForm";
	}

	/**
	 * 개인정보 수정 처리
	 로그인 안 한 사람은 오류가 나지 왜냐면 아이디가 널값이니까, */
	@RequestMapping (value="update", method=RequestMethod.POST)
	public String update(Member member, Model model, HttpSession session) {
		String loginId = (String) session.getAttribute("loginId");
		member.setId(loginId);;
		logger.debug("수정 데이터 : {}", member);
		
		int res = dao.updateMember(member);
		if (res != 0) {
			return "redirect:/";
		}
		else {
			return "memberjsp/updateForm";
		}
	}

}
